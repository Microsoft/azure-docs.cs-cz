---
title: Prostředky služby Azure Automation v řešení pro správu | Microsoft Docs
description: Řešení pro správu by měl obvykle zahrnovat sady runbook ve službě Azure Automation k automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Tento článek popisuje, jak se zahrnuje sady runbook a jejich související prostředky v řešení.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5750cd1147ec861ea38ff2ebc9ce481d256c1959
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Přidání prostředky Azure Automation do řešení pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma popsané níže se mohou změnit.   


[Řešení pro správu](operations-management-suite-solutions.md) by měl obvykle zahrnovat sady runbook ve službě Azure Automation k automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Účty Automation kromě sady runbook, obsahuje prostředky, jako jsou proměnné a plány, které podporují sady runbook používá v řešení.  Tento článek popisuje, jak se zahrnuje sady runbook a jejich související prostředky v řešení.

> [!NOTE]
> Ukázky v tomto článku použít parametry a proměnné, které jsou nutné nebo společné pro řešení pro správu a jsou popsány v [návrhu a sestavení řešení pro správu v Azure ](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s následujícími informacemi.

- Postup [vytvoření řešení správy](operations-management-suite-solutions-creating.md).
- Struktura [soubor řešení](operations-management-suite-solutions-solution-file.md).
- Postup [vytváření šablon Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Účet Automation
Všechny prostředky ve službě Azure Automation jsou součástí [účet Automation](../automation/automation-security-overview.md#automation-account-overview).  Jak je popsáno v [og pracovní prostor analýzy a účet Automation](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account) účet Automation není zahrnutý v řešení pro správu, ale musí existovat před instalací řešení.  Pokud není k dispozici, se nezdaří instalace řešení.

Název každého prostředku automatizace obsahuje název svůj účet Automation.  To se provádí v řešení s **accountName** parametr jako v následujícím příkladu runbook prostředku.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooky
By měly obsahovat všechny sady runbook používá řešení v souboru řešení tak, aby se vytváří při instalaci řešení.  Text sady runbook v šabloně nemůže obsahovat Přestože, proto byste měli publikovat sadu runbook na veřejném místě, kde je přístupná žádný uživatel instalaci řešení.

[Azure Automation runbook](../automation/automation-runbook-types.md) prostředky mít typ **Microsoft.Automation/automationAccounts/runbooks** a mít následující strukturu. Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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
| runbookType |Určuje typy sady runbook. <br><br> Skript - skript prostředí PowerShell <br>PowerShell – pracovní postup prostředí PowerShell <br> GraphPowerShell - runbook skriptu grafické prostředí PowerShell <br> GraphPowerShellWorkflow - runbook pracovního postupu grafické prostředí PowerShell |
| logProgress |Určuje, zda [průběhu záznamy](../automation/automation-runbook-output-and-messages.md) by měl být vygenerován pro sadu runbook. |
| logVerbose |Určuje, zda [podrobných záznamů](../automation/automation-runbook-output-and-messages.md) by měl být vygenerován pro sadu runbook. |
| description |Volitelný popis pro sadu runbook. |
| publishContentLink |Určuje obsah sady runbook. <br><br>identifikátor URI – identifikátor Uri, který obsah sady runbook.  Bude jím souboru s příponou .ps1 pro prostředí PowerShell a skript sady runbook a soubor exportovaný grafický runbook pro sadu runbook grafu.  <br> verze - verze sady runbook pro vlastní sledování. |


## <a name="automation-jobs"></a>Automatizace úloh
Když spustíte runbook ve službě Azure Automation, vytvoří úlohu automatizace.  Prostředek automatizace úloh můžete přidat do vašeho řešení na automatické spuštění sady runbook, když je nainstalován do řešení pro správu.  Tato metoda se obvykle používá ke spuštění sady runbook, které se používají pro počáteční konfiguraci řešení.  Chcete-li spuštění sady runbook v pravidelných intervalech, vytvořte [plán](#schedules) a [plán úloh](#job-schedules)

Úloha prostředky mít typ **Microsoft.Automation/automationAccounts/jobs** a mít následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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

Vlastnosti pro automatizaci úloh jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| sady runbook |Jeden název entity s názvem runbook spustit. |
| parameters |Entita pro každou hodnotu parametru vyžaduje sadu runbook. |

Úloha obsahuje název sady runbook a všechny hodnoty parametrů pro odeslání do runbooku.  Úloha by měla [závisí na](operations-management-suite-solutions-solution-file.md#resources) před úlohy je třeba vytvořit runbook, která se spouští od runbooku.  Pokud máte více sad runbook, který by měl být spuštěn můžete definovat jejich pořadí tak, že úloha závisí na jiné úlohy, které by měl být spuštěn první.

Název prostředku úlohy musí obsahovat identifikátor GUID, který je obvykle přiřadila parametr.  Další informace o parametrech identifikátor GUID v [vytvoření souboru sady management řešení v Azure](operations-management-suite-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikáty
[Azure Automation certifikáty](../automation/automation-certificates.md) mít typ **Microsoft.Automation/automationAccounts/certificates** a mít následující strukturu. Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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
| base64Value |Hodnota Base 64 pro certifikát. |
| Kryptografický otisk |Kryptografický otisk certifikátu. |



## <a name="credentials"></a>Přihlašovací údaje
[Přihlašovací údaje Azure Automation](../automation/automation-credentials.md) mít typ **Microsoft.Automation/automationAccounts/credentials** a mít následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 


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

Vlastnosti přihlašovacích údajů prostředky jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| userName |Uživatelské jméno pro přihlašovací údaje. |
| heslo |Heslo pro přihlašovací údaje. |


## <a name="schedules"></a>Plány
[Azure Automation plány](../automation/automation-schedules.md) mít typ **Microsoft.Automation/automationAccounts/schedules** a mají následující strukturu. Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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
| description |Volitelný popis pro plán. |
| startTime |Určuje počáteční čas plánu jako objekt data a času. Řetězec lze zadat, pokud je možné ji převést na platný datový typ DateTime. |
| isEnabled |Určuje, zda je povoleno plán. |
| interval |Typ intervalu pro plán.<br><br>den<br>hodina |
| frequency |Četnost plán by měl fire počtu dnů nebo hodin. |

Plány musí mít počáteční čas s hodnotou větší než aktuální čas.  Tuto hodnotu nelze poskytnout proměnné, vzhledem k tomu, že by měla mít žádný způsob, jak zjistit, kdy se bude nainstalována.

Při použití plánu prostředky v řešení, použijte jednu z následujících dvou strategií.

- Použijte parametr pro čas spuštění plánu.  To se zobrazí výzva k zadání hodnoty při instalaci řešení.  Pokud máte více plánů, můžete použít jeden parametr hodnotu pro více než jeden z nich.
- Vytvořte plány pomocí sady runbook, který se spustí při řešení je nainstalováno.  To eliminuje požadavek uživatele na určují dobu, ale nemůže obsahovat plán ve vašem řešení, když dojde k odebrání řešení bude odebrán.


### <a name="job-schedules"></a>Plány úlohy
Prostředky plán úlohy propojit sady runbook s plánem.  Mají typ **Microsoft.Automation/automationAccounts/jobSchedules** a mají následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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


Vlastnosti pro plány úloh jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Název plánu |Jeden **název** entity s názvem plánu. |
| Název sady Runbook  |Jeden **název** entity se název sady runbook.  |



## <a name="variables"></a>Proměnné
[Azure Automation proměnné](../automation/automation-variables.md) mít typ **Microsoft.Automation/automationAccounts/variables** a mít následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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
| isEncrypted | Určuje, jestli by se proměnná šifrovat. |
| type | Tato vlastnost aktuálně nemá žádný vliv.  Datový typ proměnné určí počáteční hodnota. |
| hodnota | Hodnota proměnné. |

> [!NOTE]
> **Typ** vlastnost aktuálně nemá žádný vliv na proměnnou vytváří.  Hodnota závisí na datový typ pro proměnnou.  

Pokud jste nastavili počáteční hodnotu pro proměnnou, musí být nakonfigurované jako správného datového typu.  Následující tabulka obsahuje různé datové typy, které jsou povolené a jejich syntaxi.  Všimněte si, že se hodnoty ve formátu JSON očekává vždycky být uzavřena v uvozovkách s žádné speciální znaky v rámci uvozovky.  Například by být řetězcová hodnota určena řetězec v uvozovkách (pomocí řídicí znak (\\)) číselnou hodnotu by zadán s jednu sadu uvozovky.

| Typ dat | Popis | Příklad: | Přeloží na |
|:--|:--|:--|:--|
| řetězec   | Vložte hodnotu do dvojitých uvozovek.  | "\"Hello, world\"" | "Hello, world" |
| číselné  | Číselná hodnota se jednoduchých uvozovkách.| "64" | 64 |
| Boolean  | **Hodnota TRUE,** nebo **false** v uvozovkách.  Všimněte si, že tato hodnota musí být malými písmeny. | "true" | true (pravda) |
| datetime | Hodnota serializovaná data.<br>Můžete použít rutinu ConvertTo-Json v prostředí PowerShell k vygenerování této hodnoty pro konkrétní datum.<br>Příklad: get datum "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduly
Řešení pro správu není nutné definovat [globální moduly](../automation/automation-integration-modules.md) použít ve vašich sadách runbook, protože se budou vždy k dispozici ve vašem účtu Automation.  Musíte zahrnout prostředku pro ostatní moduly používané vaší sady runbook.

[Integrační moduly](../automation/automation-integration-modules.md) mít typ **Microsoft.Automation/automationAccounts/modules** a mít následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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


Vlastnosti modulu prostředky jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| contentLink |Určuje obsah modulu. <br><br>identifikátor URI – identifikátor Uri obsahu modulu.  Bude jím souboru s příponou .ps1 pro prostředí PowerShell a skript sady runbook a soubor exportovaný grafický runbook pro sadu runbook grafu.  <br> verze - verze modulu pro vlastní sledování. |

Sada runbook by měl závisí na modulu prostředků a ověřte, že je vytvořen před sady runbook.

### <a name="updating-modules"></a>Aktualizace moduly
Pokud aktualizujete řešení pro správu, která zahrnuje sadu runbook, která používá plánu a novou verzi řešení má nové modulu používá dané sady runbook, může používat runbook stará verze modulu.  Musí zahrnovat následující sady runbook ve vašem řešení a vytvořit úlohu, abyste je mohli spustit před jiné runbooky.  To zajistí, že jsou všechny moduly, aktualizovat, protože požadované než sady runbook jsou načtena.

* [Aktualizace ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) zajistí, že jsou všechny moduly používané v sadách runbook ve vašem řešení na nejnovější verzi.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) bude znovu registrovat všechny prostředky plán zajistit, že sady runbook s nimi propojené s použitím nejnovější moduly.




## <a name="sample"></a>Ukázka
Tady je ukázka řešení, které zahrnují, který obsahuje následující zdroje:

- Sady Runbook.  Toto je uložen v úložišti GitHub, které veřejné vzorové sady runbook.
- Úlohu automatizace, který se spustí sada runbook při řešení je nainstalováno.
- Plán a plán úlohy pro spuštění sady runbook v pravidelných intervalech.
- Certifikát.
- Přihlašovací údaje.
- Proměnná.
- Modul.  Toto je [OMSIngestionAPI modulu](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) pro zápis dat k analýze protokolů. 

Příklad používá [standardní řešení parametry](operations-management-suite-solutions-solution-file.md#parameters) proměnné, které by běžně používané v řešení oproti hardcoding hodnoty v definicích prostředků.


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
* [Přidat zobrazení do řešení](operations-management-suite-solutions-resources-views.md) k vizualizaci shromážděná data.
