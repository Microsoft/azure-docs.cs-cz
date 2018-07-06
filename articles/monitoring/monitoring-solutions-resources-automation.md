---
title: Prostředky Azure Automation v řešení pro správu | Dokumentace Microsoftu
description: Řešení pro správu obvykle zahrnují sady runbook ve službě Azure Automation pro automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Tento článek popisuje, jak zahrnout do řešení sady runbook a jejich souvisejících prostředcích.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95d5b2499f9e260e6ed134c4191b053325ca3f42
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868818"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Přidání prostředků služby Azure Automation do řešení pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.   


[Řešení pro správu]( monitoring-solutions.md) by měl obvykle zahrnovat sady runbook ve službě Azure Automation pro automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Kromě runbooků účty služby Automation obsahuje prostředky, jako jsou proměnné a plány, které podporoval runbooky, které používají v řešení.  Tento článek popisuje, jak zahrnout do řešení sady runbook a jejich souvisejících prostředcích.

> [!NOTE]
> Ukázky v tomto článku použijte parametry a proměnné, které jsou povinné nebo společné pro řešení pro správu a jsou popsány v [návrh a sestavení řešení pro správu v Azure ]( monitoring-solutions-creating.md) 


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s následujícími informacemi.

- Jak [vytvořte řešení pro správu]( monitoring-solutions-creating.md).
- Struktura [soubor řešení]( monitoring-solutions-solution-file.md).
- Jak [vytváření šablon Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Účet Automation
Všechny prostředky ve službě Azure Automation jsou součástí [účtu Automation](../automation/automation-security-overview.md#automation-account-overview).  Jak je popsáno v [pracovní prostor Log Analytics a účet Automation]( monitoring-solutions.md#log-analytics-workspace-and-automation-account) není zahrnutý v řešení pro správu účtu služby Automation, ale musí existovat před instalací řešení.  Pokud není k dispozici, se nezdaří instalace řešení.

Název každého prostředku automatizace obsahuje název jeho účet služby Automation.  To se provádí v řešení se **accountName** parametr jako v následujícím příkladu runbook prostředku.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooky
Měli byste zahrnout všechny runbooky tak, aby byla vytvořena při instalaci řešení používá řešení v souboru řešení.  Tělo dané sady runbook v šabloně nemůže obsahovat však, měli byste Publikovat sadu runbook na veřejné umístění, kde byla přístupná libovolným uživatelem instalaci vašeho řešení.

[Azure Automation runbook](../automation/automation-runbook-types.md) prostředky mají typ **Microsoft.Automation/automationAccounts/runbooks** a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Vlastnosti pro sady runbook jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| runbookType |Určuje typy sady runbook. <br><br> Skript – skript prostředí PowerShell <br>PowerShell – pracovního postupu Powershellu <br> GraphPowerShell – grafický Powershellový skript runbook <br> GraphPowerShellWorkflow – grafický Powershellový pracovní postup runbook |
| logProgress |Určuje, zda [záznamy o průběhu](../automation/automation-runbook-output-and-messages.md) by měl být vygenerován pro sadu runbook. |
| logVerbose |Určuje, zda [podrobné záznamy](../automation/automation-runbook-output-and-messages.md) by měl být vygenerován pro sadu runbook. |
| description |Volitelný popis pro sadu runbook. |
| publishContentLink |Určuje obsah sady runbook. <br><br>identifikátor URI - Uri, který se obsah sady runbook.  Bude jím soubor .ps1 pro sady runbook Powershellu a skriptu a souboru exportované grafický runbook pro sadu runbook graf.  <br> verze – verze sady runbook pro vlastní sledování. |


## <a name="automation-jobs"></a>Úloh služby Automation
Při spuštění runbooku ve službě Azure Automation, vytvoří úlohu služby automation.  Prostředek úloh služby automation můžete přidat do vašeho řešení na automatické spuštění sady runbook při instalaci řešení pro správu.  Tato metoda se obvykle používá ke spuštění sady runbook, které se používají pro počáteční konfiguraci řešení.  Chcete-li spustit sadu runbook v pravidelných intervalech, vytvořte [plán](#schedules) a [plán úlohy](#job-schedules)

Prostředky úlohy mají typ **Microsoft.Automation/automationAccounts/jobs** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Vlastnosti pro automatizaci úloh jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| sady runbook |Jeden název entity s názvem spuštění sady runbook. |
| parameters |Entitu pro každou hodnotu parametru vyžaduje sadu runbook. |

Úloha obsahuje název sady runbook a všechny hodnoty parametrů pro odeslaný do runbooku.  Úloha by měla [závisí na]( monitoring-solutions-solution-file.md#resources) runbook, který se spouští od sady runbook musí být vytvořen před skončením úlohy.  Pokud máte více sad runbook, který by měl být spuštěn můžete definovat jejich pořadí tak, že úloha závisí na jiné úlohy, které by se měl spustit první.

Název prostředku úlohy musí obsahovat identifikátor GUID, které je přiřazeno obvykle parametrem.  Další informace o parametrech identifikátor GUID v [vytváření souboru řešení správy v Azure]( monitoring-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikáty
[Azure Automation certifikáty](../automation/automation-certificates.md) mají typ **Microsoft.Automation/automationAccounts/certificates** a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



Vlastnosti pro certifikáty prostředky jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| base64Value |Hodnoty Base 64 pro certifikát. |
| Kryptografický otisk |Kryptografický otisk certifikátu. |



## <a name="credentials"></a>Přihlašovací údaje
[Přihlašovací údaje Azure Automation](../automation/automation-credentials.md) mají typ **Microsoft.Automation/automationAccounts/credentials** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

Vlastnosti Credential zdroje jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| uživatelské jméno |Uživatelské jméno pro přihlašovací údaje. |
| heslo |Heslo pro přihlašovací údaje. |


## <a name="schedules"></a>Plány
[Plány služeb automatizace Azure](../automation/automation-schedules.md) mají typ **Microsoft.Automation/automationAccounts/schedules** a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Vlastnosti pro plán prostředky jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| description |Volitelný popis pro daný plán. |
| startTime |Určuje počáteční čas plánu jako objekt DateTime. Řetězec lze zadat, pokud je možné převést na platný DateTime. |
| hodnotu isEnabled |Určuje, zda je povolena v plánu. |
| interval |Typ intervalu pro daný plán.<br><br>den<br>hodina |
| frequency |Četnost plán by měl vyvolat za počet dnů nebo hodin. |

Plány musí mít počáteční čas s hodnotou větší než aktuální čas.  Tato hodnota nemůže poskytnout proměnnou, vzhledem k tomu, že byste měli vědět, kdy se chystá k instalaci.

Při použití plánu prostředků v řešení, použijte jednu z následujících dvou strategií.

- Parametr lze použijte pro čas spuštění plánu.  Tím se zobrazí výzva k zadání hodnoty při instalaci řešení.  Pokud máte více plánů, můžete použít hodnotu jednoho parametru pro více než jeden z nich.
- Vytvořte plány pomocí sady runbook, která se spustí, když je řešení nainstalováno.  To eliminuje nutnost uživatele, jak určit čas, ale nemůže obsahovat plán ve vašem řešení, tak se odebere po odebrání řešení.


### <a name="job-schedules"></a>Plány úlohy
Prostředky plánu úlohy propojení sady runbook s plánem.  Mají typ **Microsoft.Automation/automationAccounts/jobSchedules** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


Vlastnosti pro plány úloh jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Název plánu |Jeden **název** entitě s názvem podle plánu. |
| Název sady runbook  |Jeden **název** entitě s názvem sady runbook.  |



## <a name="variables"></a>Proměnné
[Azure Automation proměnné](../automation/automation-variables.md) mají typ **Microsoft.Automation/automationAccounts/variables** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

Vlastnosti pro proměnné prostředky jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| description | Volitelný popis pro proměnnou. |
| isEncrypted | Určuje, jestli by měl být šifrovaná proměnná. |
| type | Tato vlastnost aktuálně nemá žádný vliv.  Datový typ proměnné určí počáteční hodnota. |
| hodnota | Hodnota proměnné. |

> [!NOTE]
> **Typ** vlastnost aktuálně nemá žádný vliv na proměnné vytváří.  Datový typ pro proměnnou určí hodnotu.  

Pokud jste nastavili počáteční hodnotu pro proměnnou, musí nakonfigurovat na správného datového typu.  Následující tabulka obsahuje různé datové typy, které jsou povolené a jejich syntaxi.  Všimněte si, že se hodnoty ve formátu JSON očekává vždy být uzavřen v uvozovkách s žádné speciální znaky v uvozovkách.  Například by se zadal hodnotu řetězce podle uvozovky kolem řetězce (pomocí řídicí znak (\\)) zatímco číselná hodnota by se zadal s jednou sadou uvozovky.

| Typ dat | Popis | Příklad: | Řeší na |
|:--|:--|:--|:--|
| řetězec   | Hodnota uzavřete do dvojitých uvozovek.  | "\"Hello world\"" | "Hello world" |
| číselné  | Číselná hodnota v jednoduchých uvozovkách.| "64" | 64 |
| Boolean  | **Hodnota TRUE** nebo **false** v uvozovkách.  Všimněte si, že tato hodnota musí obsahovat malá písmena. | "true" | true (pravda) |
| datetime | Hodnota serializovaná data.<br>Rutiny ConvertTo-Json v prostředí PowerShell můžete použít k vygenerování této hodnoty pro konkrétní datum.<br>Příklad: get datum "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduly
Řešení pro správu není nutné definovat [globální moduly](../automation/automation-integration-modules.md) použít ve vašich sadách runbook, protože se bude mít vždycky k dispozici ve vašem účtu Automation.  Je nutné pro zahrnutí prostředků pro ostatní moduly používané vaší sady runbook.

[Integrační moduly](../automation/automation-integration-modules.md) mají typ **Microsoft.Automation/automationAccounts/modules** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


Vlastnosti modulu prostředky jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| contentLink |Určuje obsah modulu. <br><br>identifikátor URI – identifikátor Uri obsahu modulu.  Bude jím soubor .ps1 pro sady runbook Powershellu a skriptu a souboru exportované grafický runbook pro sadu runbook graf.  <br> verze – verze modulu pro vlastní sledování. |

Runbook by měl záviset na modulu prostředků a ověřte, že je vytvořen před sady runbook.

### <a name="updating-modules"></a>Aktualizace modulů
Pokud aktualizujete řešení pro správu, který obsahuje sadu runbook, která používá plánu a novou verzi vašeho řešení se nový modul používaný dané sady runbook, může sada runbook používají starší verzi modulu.  By měl obsahovat následující sady runbook ve vašem řešení a vytvořte úlohu pro spuštění před všechny runbooky.  Tím se zajistí, že se aktualizovaly všechny moduly, které vyžaduje předtím, než se načítají sady runbook.

* [Aktualizace ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) zajistí, že jsou všechny moduly používané v sadách runbook ve vašem řešení na nejnovější verzi.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) bude znovu zaregistrujte všechny prostředky plánu zajistit, že runbooky propojené s jejich s použitím nejnovější moduly.




## <a name="sample"></a>Ukázka
Tady je příklad řešení, které zahrnují, který obsahuje následující zdroje:

- Sady Runbook.  Toto je vzorový runbook uložena ve veřejném úložišti Githubu.
- Úlohy automatizace, která spustí sadu runbook, když je řešení nainstalováno.
- Plán a plán úlohy pro spuštění sady runbook v pravidelných intervalech.
- Certifikát.
- Přihlašovací údaje.
- Proměnná.
- Modul.  Toto je [OMSIngestionAPI modulu](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) za zápis dat do Log Analytics. 

Ukázka používá [standardní řešení parametry]( monitoring-solutions-solution-file.md#parameters) proměnné, které používají ho většinou v řešení, na rozdíl od hodnoty hardcoding v definicích prostředků.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Další postup
* [Přidání zobrazení do vašeho řešení]( monitoring-solutions-resources-views.md) vizualizovat shromážděná data.
