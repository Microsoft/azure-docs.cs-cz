---
title: Správa rozšíření virtuálních počítačů pomocí serverů s podporou ARC Azure
description: Servery s podporou ARC Azure můžou spravovat nasazení rozšíření virtuálních počítačů, která poskytují konfiguraci po nasazení a úlohy automatizace s virtuálními počítači mimo Azure.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 988c4d7b2fcbffb95932fe70d8014de74dd33343
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887746"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Správa rozšíření virtuálních počítačů pomocí serverů s podporou ARC Azure

Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače.

Servery s podporou ARC Azure vám umožňují nasadit rozšíření virtuálních počítačů Azure do virtuálních počítačů s jiným než Azure a Linux, což zjednodušuje správu hybridního počítače v místním prostředí, hraničních zařízeních a dalších cloudových prostředích v životním cyklu.

## <a name="key-benefits"></a>Klíčové výhody

Podpora rozšíření virtuálních počítačů serverů s podporou ARC Azure nabízí následující klíčové výhody:

* [Konfigurace stavu Azure Automation](../../automation/automation-dsc-overview.md) slouží k centrálnímu ukládání konfigurací a údržbě požadovaného stavu hybridních připojených počítačů povolených prostřednictvím rozšíření virtuálního počítače DSC.

* Shromažďovat data protokolu pro analýzu s [protokoly v Azure monitor](../../azure-monitor/platform/data-platform-logs.md) povolené prostřednictvím rozšíření virtuálního počítače agenta Log Analytics. To je užitečné při provádění složitých analýz napříč daty z nejrůznějších zdrojů.

* Díky [Azure monitor pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md)analyzuje výkon virtuálních počítačů s Windows a Linux a monitoruje jejich procesy a závislosti na dalších prostředcích a externích procesech. Toho dosáhnete tím, že povolíte rozšíření Log Analytics agenta a rozšíření virtuálního počítače agenta závislostí.

* Stahovat a spouštět skripty na hybridních připojených počítačích pomocí rozšíření vlastních skriptů. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy.

## <a name="availability"></a>Dostupnost

Funkce rozšíření virtuálních počítačů jsou k dispozici pouze v seznamu [podporovaných oblastí](overview.md#supported-regions). Ujistěte se, že jste počítač připojili do jedné z těchto oblastí.

## <a name="extensions"></a>Rozšíření

V této verzi Preview podporujeme následující rozšíření virtuálních počítačů na počítačích s Windows a Linux.

|Rozšíření |Operační systém |Publisher |Další informace |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Rozšíření vlastních skriptů pro Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Rozšíření Windows PowerShell DSC](../../virtual-machines/extensions/dsc-windows.md)|
|Agent Log Analytics |Windows |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics rozšíření virtuálního počítače pro Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Rozšíření pro virtuální počítače s agentem závislosti pro Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Rozšíření vlastních skriptů pro Linux verze 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[Rozšíření PowerShell DSC pro Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agent Log Analytics |Linux |Microsoft. EnterpriseCloud. Monitoring |[Rozšíření virtuálního počítače s Log Analytics pro Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Rozšíření pro virtuální počítače s agentem závislosti pro Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Rozšíření virtuálních počítačů je možné spouštět pomocí šablon Azure Resource Manager, z Azure Portal nebo Azure PowerShell na hybridních serverech spravovaných servery s podporou ARC.

Další informace o balíčku agenta připojeného počítače Azure a podrobnostech o komponentě agenta rozšíření najdete v tématu [Přehled agenta](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na následujících poskytovatelích prostředků Azure v rámci vašeho předplatného:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Pokud ještě nejsou zaregistrované, postupujte podle kroků v části [registrace poskytovatelů prostředků Azure](agent-overview.md#register-azure-resource-providers).

Rozšíření virtuálního počítače agenta Log Analytics pro Linux vyžaduje Python 2. x na cílovém počítači.

### <a name="connected-machine-agent"></a>Agent připojeného počítače

Ověřte, že počítač odpovídá [podporovaným verzím](agent-overview.md#supported-operating-systems) operačního systému Windows a Linux pro agenta připojeného počítače Azure.

Minimální verze agenta připojeného počítače, který je podporován touto funkcí:

* Windows – 0,7. x
* Linux-0,8. x

Pokud chcete upgradovat počítač na požadovanou verzi agenta, přečtěte si téma [Upgrade agenta](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Povolení rozšíření z portálu

Rozšíření virtuálních počítačů můžete použít k použití ARC pro serverový počítač spravovaný pomocí Azure Portal.

1. V prohlížeči přejdete na [Azure Portal](https://aka.ms/arcserver-preview).

2. Na portálu přejděte na **servery – Azure ARC** a ze seznamu vyberte svůj hybridní počítač.

3. Vyberte **rozšíření**a pak **Přidat**. V seznamu dostupných rozšíření vyberte požadované rozšíření a postupujte podle pokynů v průvodci. V tomto příkladu nasadíme rozšíření Log Analytics VM.

    ![Vybrat rozšíření virtuálního počítače pro vybraný počítač](./media/manage-vm-extensions/add-vm-extensions.png)

    Následující příklad ukazuje instalaci rozšíření Log Analytics virtuálního počítače z Azure Portal:

    ![Nainstalovat rozšíření virtuálního počítače Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    K dokončení instalace je nutné zadat ID a primární klíč pracovního prostoru. Pokud nejste obeznámeni s tím, jak tyto informace najít, přečtěte si téma [získání ID a klíče pracovního prostoru](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Po potvrzení požadovaných informací vyberte **vytvořit**. Zobrazí se souhrn nasazení a můžete zkontrolovat stav nasazení.

>[!NOTE]
>I když je možné dávkovat více rozšíření a zpracovat je, nainstalují se sériově. Po dokončení první instalace rozšíření dojde k pokusu o instalaci dalšího rozšíření.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů lze přidat do šablony Azure Resource Manager a spustit s nasazením šablony. S rozšířeními virtuálních počítačů podporovaných servery s podporou ARC můžete nasadit podporované rozšíření virtuálního počítače v počítačích se systémem Linux nebo Windows pomocí Azure PowerShell. Každý příklad obsahuje soubor šablony a soubor parametrů s ukázkovými hodnotami, které se mají poskytnout šabloně.

>[!NOTE]
>I když je možné dávkovat více rozšíření a zpracovat je, nainstalují se sériově. Po dokončení první instalace rozšíření dojde k pokusu o instalaci dalšího rozšíření.

### <a name="deploy-the-log-analytics-vm-extension"></a>Nasazení rozšíření Log Analytics VM

Chcete-li snadno nasadit agenta Log Analytics, je k dispozici následující ukázka pro instalaci agenta v systému Windows nebo Linux.

#### <a name="template-file-for-linux"></a>Soubor šablony pro Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Soubor šablony pro Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Soubor parametrů

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Uložte šablonu a soubory parametrů na disk a upravte soubor parametrů s odpovídajícími hodnotami pro vaše nasazení. Pak můžete rozšíření nainstalovat na všechny připojené počítače ve skupině prostředků pomocí následujícího příkazu. Příkaz pomocí parametru *TemplateFile* Určuje šablonu a parametr *TemplateParameterFile* pro určení souboru, který obsahuje parametry a hodnoty parametrů.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Nasazení rozšíření vlastních skriptů

Chcete-li použít rozšíření vlastních skriptů, je k dispozici následující ukázka pro spuštění v systému Windows a Linux. Pokud neznáte rozšíření vlastních skriptů, přečtěte si téma rozšíření [vlastních skriptů pro Windows](../../virtual-machines/extensions/custom-script-windows.md) nebo [rozšíření vlastních skriptů pro Linux](../../virtual-machines/extensions/custom-script-linux.md). Existuje několik různých charakteristik, které byste měli pochopit při používání tohoto rozšíření u hybridních počítačů:

* Seznam podporovaných operačních systémů s rozšířením vlastních skriptů virtuálního počítače Azure se nedá použít pro servery s podporou ARC Azure. Seznam podporovaných OSs pro servery s podporou ARC najdete [tady](agent-overview.md#supported-operating-systems).

* Podrobnosti o konfiguraci týkající se Azure Virtual Machine Scale Sets nebo klasických virtuálních počítačů nejsou k dispozici.

* Pokud vaše počítače potřebují stáhnout skript externě a můžou komunikovat jenom pomocí proxy server, musíte [nakonfigurovat agenta připojeného počítače](manage-agent.md#update-or-remove-proxy-settings) , aby nastavil proměnnou prostředí proxy server.

Konfigurace rozšíření vlastních skriptů určuje například umístění skriptu a příkaz, který má být spuštěn. Tato konfigurace je určena v šabloně Azure Resource Manager, která je uvedená níže pro hybridní počítače se systémy Linux a Windows.

#### <a name="template-file-for-linux"></a>Soubor šablony pro Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Soubor šablony pro Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Nasazení rozšíření PowerShell DSC

Chcete-li použít rozšíření PowerShell DSC, je k dispozici následující ukázka pro spuštění v systému Windows a Linux. Pokud neznáte rozšíření PowerShell DSC, přečtěte si téma [Přehled obslužné rutiny rozšíření DSC](../../virtual-machines/extensions/dsc-overview.md). Existuje několik různých charakteristik, které byste měli pochopit při používání tohoto rozšíření u hybridních počítačů:

* Seznam podporovaných operačních systémů s rozšířením PowerShell pro virtuální počítače Azure se nevztahují na servery s podporou ARC Azure. Seznam podporovaných OSs pro servery s podporou ARC najdete [tady](agent-overview.md#supported-operating-systems).

* Pokud vaše počítače potřebují stáhnout skript externě a můžou komunikovat jenom pomocí proxy server, musíte [nakonfigurovat agenta připojeného počítače](manage-agent.md#update-or-remove-proxy-settings) , aby nastavil proměnnou prostředí proxy server.

#### <a name="template-file-for-linux"></a>Soubor šablony pro Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Soubor šablony pro Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Soubor parametrů

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Agent závislostí

Chcete-li použít rozšíření agenta závislosti Azure Monitor, je k dispozici následující ukázka pro spuštění v systému Windows a Linux. Pokud neznáte agenta závislostí, přečtěte si téma [přehled Azure Monitorch agentů](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Soubor šablony pro Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Soubor šablony pro Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>Odinstalace rozšíření

Odebrání jedné nebo více rozšíření ze serveru s povoleným obloukem se dá provést jenom z Azure Portal. Chcete-li odebrat rozšíření, proveďte následující kroky.

1. V prohlížeči přejdete na [Azure Portal](https://portal.azure.com).

2. Na portálu přejděte na **servery – Azure ARC** a ze seznamu vyberte svůj hybridní počítač.

3. Zvolte **rozšíření**a pak vyberte rozšíření ze seznamu nainstalovaných rozšíření.

4. Vyberte možnost **odinstalovat** a když se zobrazí výzva k ověření, vyberte **Ano** a pokračujte.

## <a name="troubleshooting"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal.

Následující kroky pro řešení potíží se vztahují na všechna rozšíření virtuálních počítačů.

1. Pokud chcete zkontrolovat protokol hostovaného agenta, podívejte se na aktivitu při zřizování rozšíření `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` pro Windows a v části pro Linux v `/var/lib/GuestConfig/ext_mgr_logs` .

2. Další podrobnosti v systému Windows najdete v protokolech rozšíření pro konkrétní rozšíření `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . Výstup rozšíření se zaznamená do souboru pro každé rozšíření nainstalované v systému Linux `/var/lib/GuestConfig/extension_logs` .

3. Podívejte se na oddíly řešení potíží v dokumentaci ke konkrétnímu rozšíření pro kódy chyb, známé problémy atd. Další informace o řešení potíží pro jednotlivá rozšíření najdete v části **řešení potíží a podpora** v tématu Přehled tohoto rozšíření. To zahrnuje Popis chybových kódů zapsaných do protokolu. Články rozšíření jsou propojeny v [tabulce rozšíření](#extensions) , kterou najdete dříve v tomto článku.

4. Podívejte se na systémové protokoly. Vyhledejte další operace, které mohly být v konfliktu s rozšířením, například dlouhodobě běžící instalace jiné aplikace, která vyžadovala výhradní přístup správce balíčků.

## <a name="next-steps"></a>Další kroky

- Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

- Přečtěte si další informace o nástroji [[Log Analytics agent]](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).