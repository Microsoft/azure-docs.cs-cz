---
title: Prostředky Azure Automation v řešeních pro správu | Microsoft Docs
description: Řešení pro správu obvykle budou zahrnovat Runbooky v Azure Automation k automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Tento článek popisuje, jak zahrnout Runbooky a jejich související prostředky do řešení.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3b1b134afbc4a13d7888281a82609d444cee377
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682870"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Přidání prostředků Azure Automation do řešení pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytváření řešení pro správu, která jsou momentálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.   


[Řešení pro správu]( solutions.md) obvykle budou zahrnovat runbooky v Azure Automation k automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Kromě runbooků účty služby Automation obsahují prostředky, jako jsou proměnné a plány, které podporují Runbooky používané v řešení.  Tento článek popisuje, jak zahrnout Runbooky a jejich související prostředky do řešení.

> [!NOTE]
> V ukázkách v tomto článku se používají parametry a proměnné, které jsou buď vyžadované, nebo běžné pro řešení pro správu, popsaná v článku [Návrh a sestavení řešení pro správu v Azure]( solutions-creating.md) . 


## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že už jste obeznámeni s následujícími informacemi.

- [Vytvoření řešení pro správu]( solutions-creating.md).
- Struktura [souboru řešení]( solutions-solution-file.md).
- [Vytváření šablon Správce prostředků](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Účet Automation
Všechny prostředky v Azure Automation jsou obsaženy v [účtu Automation](../../automation/automation-security-overview.md).  Jak je popsáno v [Log Analytics pracovní prostor a účet Automation]( solutions.md#log-analytics-workspace-and-automation-account) účet Automation není zahrnutý do řešení pro správu, ale musí existovat před tím, než se řešení nainstaluje.  Pokud není k dispozici, instalace řešení se nezdaří.

Název každého prostředku automatizace zahrnuje název svého účtu Automation.  To se provádí v řešení s parametrem **účtu** , jako v následujícím příkladu prostředku sady Runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooky
Měli byste zahrnout všechny Runbooky používané řešením v souboru řešení, aby byly vytvořeny při instalaci řešení.  V šabloně nemůžete obsahovat text sady Runbook, proto byste měli sadu Runbook publikovat do veřejného umístění, kde k němu může mít uživatel, který instaluje vaše řešení.

Prostředky [sady runbook Azure Automation](../../automation/automation-runbook-types.md) mají typ **Microsoft. Automation/automationAccounts/Runbooky** a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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


Vlastnosti pro sady Runbook jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| runbookType |Určuje typy Runbooku. <br><br> Skript – skript prostředí PowerShell <br>PowerShell – pracovní postup PowerShellu <br> GraphPowerShell – sada Runbook skriptu PowerShellu <br> GraphPowerShellWorkflow – sada Runbook pracovního postupu PowerShellu |
| logProgress |Určuje, zda mají být generovány [záznamy o průběhu](../../automation/automation-runbook-output-and-messages.md) pro sadu Runbook. |
| logVerbose |Určuje, zda mají být generovány [podrobné záznamy](../../automation/automation-runbook-output-and-messages.md) pro sadu Runbook. |
| description |Volitelný popis Runbooku |
| publishContentLink |Určuje obsah Runbooku. <br><br>identifikátor URI URI k obsahu Runbooku  To bude soubor. ps1 pro PowerShell a skriptovací Runbooky a exportovaný soubor s grafickým runbookm pro Runbook grafu.  <br> verze sady Runbook pro vlastní sledování. |


## <a name="automation-jobs"></a>Úlohy služby Automation
Když spustíte Runbook v Azure Automation, vytvoří se úloha automatizace.  Do řešení můžete přidat prostředek úlohy služby Automation, který automaticky spustí sadu Runbook při instalaci řešení pro správu.  Tato metoda se obvykle používá ke spuštění sad Runbook, které se používají pro počáteční konfiguraci řešení.  Pokud chcete spustit Runbook v pravidelných intervalech, vytvořte [plán](#schedules) a [plán úlohy](#job-schedules) .

Prostředky úlohy mají typ **Microsoft. Automation/automationAccounts/Jobs** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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

Vlastnosti pro úlohy služby Automation jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| sada Runbook |Entita s jedním jménem s názvem Runbooku, který se má spustit. |
| parameters |Entita pro každou hodnotu parametru, kterou sada Runbook vyžaduje. |

Úloha zahrnuje název Runbooku a všechny hodnoty parametrů, které se mají odeslat do Runbooku.  Úloha by měla [záviset na]( solutions-solution-file.md#resources) sadě Runbook, kterou spouští od chvíle, kdy se sada Runbook musí vytvořit před úlohou.  Pokud máte více sad Runbook, které by měly být spuštěny, můžete definovat jejich pořadí tak, aby byla úloha závislá na všech dalších úlohách, které by se měly spustit jako první.

Název prostředku úlohy musí obsahovat identifikátor GUID, který je obvykle přiřazený parametrem.  Další informace o parametrech GUID si můžete přečíst v [tématu Vytvoření souboru řešení pro správu v Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikáty
[Azure Automation certifikáty](../../automation/automation-certificates.md) mají typ **Microsoft. Automation/automationAccounts/Certificates** a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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



Vlastnosti pro prostředky certifikátů jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| base64Value |Hodnota Base 64 pro certifikát |
| kryptografický |Kryptografický otisk certifikátu |



## <a name="credentials"></a>Přihlašovací údaje
[Pověření Azure Automation](../../automation/automation-credentials.md) mají typ **Microsoft. Automation/automationAccounts/přihlašovací údaje** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 


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

Vlastnosti pro prostředky přihlašovacích údajů jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| userName |Uživatelské jméno pro přihlašovací údaje |
| heslo |Heslo pro přihlašovací údaje |


## <a name="schedules"></a>Plány
[Plány Azure Automation](../../automation/automation-schedules.md) mají typ **Microsoft. Automation/automationAccounts/Schedules** a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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

Vlastnosti pro prostředky plánu jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| description |Volitelný popis plánu |
| startTime |Určuje počáteční čas plánu jako objekt DateTime. Řetězec lze zadat, pokud jej lze převést na platný typ DateTime. |
| isEnabled |Určuje, jestli je plán povolený. |
| interval |Typ intervalu pro plán.<br><br>day<br>hour |
| frequency |Frekvence, kterou by měl plán zavolávat za počet dnů nebo hodin. |

Plány musí mít čas spuštění s hodnotou vyšší než aktuální čas.  Tuto hodnotu nemůžete zadat s proměnnou, protože by vám při instalaci nevěděla žádný způsob, jak byste měli vědět.

Při používání prostředků plánu v řešení použijte jednu z následujících dvou strategií.

- Použijte parametr pro počáteční čas plánu.  Tím se uživateli zobrazí výzva k zadání hodnoty při instalaci řešení.  Pokud máte více plánů, můžete použít jednu hodnotu parametru pro více než jeden z nich.
- Vytvořte plány pomocí Runbooku, který se spustí při instalaci řešení.  Tím se odstraní požadavek uživatele, který určí čas, ale nemůžete ho ve svém řešení použít, aby se po odebrání řešení odebral.


### <a name="job-schedules"></a>Plány úlohy
Prostředky plánu úlohy propojí sadu Runbook s plánem.  Mají typ **Microsoft. Automation/automationAccounts/jobSchedules** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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
| název plánu |Entita s jedním **jménem** s názvem plánu |
| název Runbooku  |Entita s jedním **jménem** s názvem Runbooku  |



## <a name="variables"></a>Proměnné
[Proměnné Azure Automation](../../automation/automation-variables.md) mají typ **Microsoft. Automation/automationAccounts/variabless** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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

Vlastnosti prostředků proměnných jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| description | Volitelný popis proměnné. |
| isEncrypted | Určuje, zda má být proměnná zašifrovaná. |
| typ | Tato vlastnost aktuálně nemá žádný vliv.  Datový typ proměnné bude stanoven počáteční hodnotou. |
| value | Hodnota proměnné |

> [!NOTE]
> Vlastnost **Type** nemá v současné době žádný vliv na vytvořenou proměnnou.  Datový typ proměnné bude stanoven hodnotou.  

Pokud nastavíte počáteční hodnotu pro proměnnou, je nutné ji nakonfigurovat jako správný datový typ.  Následující tabulka poskytuje různé datové typy, které umožňují a jejich syntaxi.  Všimněte si, že hodnoty ve formátu JSON by měly být vždy uzavřeny v uvozovkách se všemi speciálními znaky v uvozovkách.  Například řetězcová hodnota by byla určena uvozovkami kolem řetězce (pomocí řídicího znaku ( \\ )), zatímco číselná hodnota by byla zadána s jednou sadou uvozovek.

| Datový typ | Popis | Příklad | Překládá na |
|:--|:--|:--|:--|
| řetězec   | Uzavřete hodnotu do dvojitých uvozovek.  | \"Hello World \" | Hello World |
| numerické  | Číselná hodnota s jednoduchými uvozovkami.| "64" | 64 |
| Boolean  | **hodnota true** nebo **false** v uvozovkách  Všimněte si, že tato hodnota musí být malá. | podmínka | true |
| datetime | Hodnota serializovaného data<br>K vygenerování této hodnoty pro konkrétní datum můžete použít rutinu ConvertTo-JSON v prostředí PowerShell.<br>Příklad: Get-Date "5/24/2017 13:14:57" \| ConvertTo-JSON | " \\ /Date (1495656897378) \\ /" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduly
Vaše řešení pro správu nemusí definovat [globální moduly](../../automation/automation-integration-modules.md) používané vašimi Runbooky, protože budou vždy k dispozici ve vašem účtu Automation.  Je potřeba zahrnout prostředek pro všechny ostatní moduly, které vaše Runbooky používají.

[Moduly integrace](../../automation/automation-integration-modules.md) mají typ **Microsoft. Automation/automationAccounts/modules** a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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


Vlastnosti pro prostředky modulů jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| contentLink |Určuje obsah modulu. <br><br>identifikátor URI URI k obsahu modulu  To bude soubor. ps1 pro PowerShell a skriptovací Runbooky a exportovaný soubor s grafickým runbookm pro Runbook grafu.  <br> verze modulu pro vlastní sledování. |

Sada Runbook by měla záviset na prostředku modulu, aby bylo zajištěno, že bude vytvořen před sadou Runbook.

### <a name="updating-modules"></a>Aktualizace modulů
Pokud aktualizujete řešení pro správu, které zahrnuje sadu Runbook, která používá plán, a nová verze vašeho řešení má nový modul, který tento Runbook používá, může sada Runbook použít starou verzi modulu.  Měli byste zahrnout následující Runbooky do vašeho řešení a vytvořit úlohu pro jejich spuštění před všemi ostatními sadami Runbook.  Tím se zajistí, že se všechny moduly aktualizují podle požadavků, než se Runbooky načtou.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) zajistí, že všechny moduly, které používají Runbooky ve vašem řešení, budou mít nejnovější verzi.  
* [ReRegisterAutomationSchedule-MS – Správa se znovu](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) zaregistruje na všechny prostředky plánu, aby se zajistilo, že se Runbooky, na které se vztahují, používají nejnovější moduly.




## <a name="sample"></a>Ukázka
Následuje ukázka řešení, které zahrnuje tyto prostředky:

- Runbook.  Toto je ukázkový Runbook uložený ve veřejném úložišti GitHub.
- Úloha služby Automation, která spouští Runbook při instalaci řešení
- Plán a plán úloh pro spuštění Runbooku v pravidelných intervalech.
- Certifikát.
- Pověřovací.
- Variabilní.
- Čipu.  Toto je [modul OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) pro zápis dat do Log Analytics. 

Ukázka používá [standardní proměnné parametrů řešení]( solutions-solution-file.md#parameters) , které by se běžně používaly v řešení, a to na rozdíl od hodnot zakódujeme v definicích prostředků.


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
            "Description": "Start time for schedule."
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




## <a name="next-steps"></a>Další kroky
* [Přidejte do svého řešení zobrazení]( solutions-resources-views.md) k vizualizaci shromážděných dat.
