---
title: Nasazení cloudové služby Azure (Rozšířená podpora) – šablony
description: Nasazení cloudové služby Azure (Rozšířená podpora) pomocí šablon ARM
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8804febe81afc79a4a7eadb56e8350e758ea38ba
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105506"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Nasazení cloudové služby (rozšířené podpory) pomocí šablon ARM

V tomto kurzu se dozvíte, jak vytvořit nasazení cloudové služby (rozšířené podpory) pomocí [šablon ARM](../azure-resource-manager/templates/overview.md). 

## <a name="before-you-begin"></a>Než začnete

1. Projděte si [požadavky nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora) a vytvořte přidružené prostředky.

2. Vytvořte novou skupinu prostředků pomocí [Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md) nebo [PowerShellu](../azure-resource-manager/management/manage-resource-groups-powershell.md). Tento krok je nepovinný, pokud používáte existující skupinu prostředků.

3. Vytvořte veřejnou IP adresu a nastavte vlastnost Popisek DNS pro veřejnou IP adresu. Cloud Services (Rozšířená podpora) podporuje jenom veřejné IP adresy [základní](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU. Veřejné IP adresy Standard SKU nefungují s Cloud Services.
Pokud používáte statickou IP adresu, musí se v souboru konfigurace služby (. cscfg) odkazovat jako na Vyhrazená IP adresa. Pokud používáte existující IP adresu, přeskočte tento krok a přidejte informace o IP adrese přímo do nastavení konfigurace nástroje pro vyrovnávání zatížení vaší šablony ARM.
 
4. Vytvořte nový účet úložiště pomocí [Azure Portal](../storage/common/storage-account-create.md?tabs=azure-portal) nebo [PowerShellu](../storage/common/storage-account-create.md?tabs=azure-powershell). Tento krok je nepovinný, pokud používáte existující účet úložiště.

5. Nahrajte soubory definice služby (. csdef) a konfigurace služby (. cscfg) do účtu úložiště pomocí [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), [AzCopy](../storage/common/storage-use-azcopy-blobs-upload.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [PowerShellu](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container). Získejte identifikátory URI SAS obou souborů, které mají být přidány do šablony ARM dále v tomto kurzu.

6. Volitelné Vytvořte Trezor klíčů a nahrajte certifikáty.

    -  Certifikáty mohou být připojeny ke cloudovým službám, aby bylo možné zabezpečenou komunikaci do a ze služby. Aby bylo možné používat certifikáty, musí být jejich kryptografické otisky zadány v souboru konfigurace služby (. cscfg) a odeslány do trezoru klíčů. Trezor klíčů je možné vytvořit pomocí [Azure Portal](../key-vault/general/quick-create-portal.md) nebo [PowerShellu](../key-vault/general/quick-create-powershell.md).
    - Přidružený Trezor klíčů musí být umístěný ve stejné oblasti a předplatném jako cloudová služba.
    - K přidruženému trezoru klíčů pro musí být povolená příslušná oprávnění, aby mohl prostředek Cloud Services (Rozšířená podpora) získat certifikáty z Key Vault. Další informace najdete v tématu [certifikáty a Key Vault](certificates-and-key-vault.md)
    - Na Trezor klíčů se musí odkazovat v části OsProfile šablony ARM uvedené v následujících krocích.

## <a name="deploy-a-cloud-service-extended-support"></a>Nasazení cloudové služby (Rozšířená podpora)

> [!NOTE]
> Snadnější a rychlejší způsob, jak vygenerovat šablonu ARM a soubor parametrů, je prostřednictvím [Azure Portal](https://portal.azure.com). Vygenerovanou [šablonu ARM](generate-template-portal.md) si můžete stáhnout přes portál a vytvořit tak cloudovou službu přes PowerShell.
 
1. Vytvořte virtuální síť. Název virtuální sítě musí odpovídat odkazům v souboru konfigurace služby (. cscfg). Pokud používáte existující virtuální síť, vynechejte tento oddíl ze šablony ARM.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Pokud vytváříte novou virtuální síť, přidejte do `dependsOn` části následující. tím zajistíte, že platforma vytvoří virtuální síť před vytvořením cloudové služby.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Vytvořte veřejnou IP adresu a (volitelně) nastavte vlastnost Popisek DNS veřejné IP adresy. Pokud používáte statickou IP adresu, musíte na ni odkazovat jako na Vyhrazená IP adresa v souboru konfigurace služby (. cscfg). Pokud používáte existující IP adresu, přeskočte tento krok a přidejte informace o IP adrese přímo do nastavení konfigurace nástroje pro vyrovnávání zatížení vaší šablony ARM.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Pokud vytváříte novou IP adresu, přidejte následující `dependsOn` část do části, abyste zajistili, že platforma vytvoří IP adresu před vytvořením cloudové služby.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Vytvořte objekt profilu sítě a přidružte veřejnou IP adresu k front-endu nástroje pro vyrovnávání zatížení. Tato platforma automaticky vytvoří nástroj pro vyrovnávání zatížení.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Přidejte odkaz na Trezor klíčů v  `OsProfile`   části šablony ARM. Key Vault slouží k ukládání certifikátů, které jsou přidruženy k Cloud Services (Rozšířená podpora). Přidejte certifikáty do Key Vault a pak v souboru konfigurace služby (. cscfg) odkázat na kryptografické otisky certifikátů. Musíte taky povolit Key Vault zásady přístupu pro Azure Virtual Machines for Deployment (na portálu), aby Cloud Services (Rozšířená podpora) mohl načíst certifikát uložený jako tajné klíče z Key Vault. Trezor klíčů musí být umístěný ve stejné oblasti a předplatném jako cloudová služba a musí mít jedinečný název. Další informace najdete v tématu [používání certifikátů s Cloud Services (Rozšířená podpora)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault je ID prostředku ARM k vašemu trezoru klíčů. Tyto informace najdete tak, že v části vlastnosti vašeho trezoru klíčů vyhledáte ID prostředku.
    > - certificateUrl můžete najít tak, že přejdete na certifikát v trezoru klíčů označený jako **tajný identifikátor**.  
   >  - certificateUrl by měla mít formu https://{endpoin}/tajné klíče/{tajného kódu}/{Secret-ID}.

5. Vytvořte profil role. Ujistěte se, že počet rolí, názvy rolí a počet instancí v každé roli a velikosti jsou stejné jako v části Konfigurace služby (. cscfg), definice služby (. csdef) a profil role v šabloně ARM.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. Volitelné Vytvořte profil rozšíření pro přidání rozšíření do cloudové služby. V tomto příkladu přidáme rozšíření Remote Desktop a Windows Azure Diagnostics.
   > [!Note] 
   > Heslo pro vzdálenou plochu musí být delší než 8-123 znaků a musí splňovat alespoň 3 požadavky na složitost hesla z následujících hodnot: 1) obsahuje velké písmeno 2) obsahuje malé písmeno 3), které obsahuje číselnou číslici 4). obsahuje speciální znak 5) řídicí znaky nejsou povoleny.

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Projděte si úplnou šablonu.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Nasaďte šablonu a soubor parametrů (definováním parametrů v souboru šablony) a vytvořte tak nasazení cloudové služby (rozšířené podpory). Podle potřeby prosím použijte tyto [vzorové šablony](https://github.com/Azure-Samples/cloud-services-extended-support) .

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Další kroky 

- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
- Navštivte [úložiště ukázek Cloud Services (rozšířené podpory)](https://github.com/Azure-Samples/cloud-services-extended-support) .
