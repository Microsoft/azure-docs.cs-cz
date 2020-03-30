---
title: Prostředky Azure Automation v řešeních pro správu | Dokumenty společnosti Microsoft
description: Řešení pro správu obvykle zahrnují runbooky v Azure Automation pro automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Tento článek popisuje, jak zahrnout sady Runbook a jejich související prostředky do řešení.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef9f27546e9db95d5a41769e1b5bc7bc0c2f851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663058"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Přidání prostředků Azure Automation do řešení pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.   


[Řešení pro správu]( solutions.md) obvykle zahrnují runbooky v Azure Automation pro automatizaci procesů, jako je shromažďování a zpracování dat monitorování.  Kromě runbooků zahrnují účty Automation prostředky, jako jsou proměnné a plány, které podporují sady Runbook používané v řešení.  Tento článek popisuje, jak zahrnout sady Runbook a jejich související prostředky do řešení.

> [!NOTE]
> Ukázky v tomto článku používají parametry a proměnné, které jsou povinné nebo společné pro řešení správy a popsané v [návrhu a sestavení řešení pro správu v Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s následujícími informacemi.

- Jak [vytvořit řešení pro správu]( solutions-creating.md).
- Struktura [souboru řešení]( solutions-solution-file.md).
- Jak [vytvořit šablony Správce prostředků](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Účet Automation
Všechny prostředky v Azure Automation jsou obsaženy v [účtu Automation](../../automation/automation-security-overview.md#automation-account-overview).  Jak je popsáno v [pracovním prostoru Log Analytics a účet automatizace]( solutions.md#log-analytics-workspace-and-automation-account) účet automatizace není součástí řešení pro správu, ale musí existovat před instalací řešení.  Pokud není k dispozici, instalace řešení se nezdaří.

Název každého prostředku automatizace obsahuje název jeho účtu automatizace.  To se provádí v řešení s **parametrem accountName** jako v následujícím příkladu prostředku runbooku.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooky
Do souboru řešení byste měli zahrnout všechny sady Runbook, které řešení používá, aby byly vytvořeny při instalaci řešení.  Nemůžete obsahovat tělo runbooku v šabloně, takže byste měli publikovat runbook do veřejného umístění, kde k němu může přistupovat každý uživatel, který instaluje vaše řešení.

[Prostředky runbooku Azure Automation](../../automation/automation-runbook-types.md) mají typ **Microsoft.Automation/automationAccounts/runbook** a mají následující strukturu. To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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


Vlastnosti runbooků jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| runbookType |Určuje typy runbooku. <br><br> Skript – skript prostředí PowerShell <br>PowerShell – pracovní postup PowerShellu <br> GraphPowerShell - Grafická aplikace runbook skriptu PowerShell <br> GraphPowerShellWorkflow – grafická pracovní kancelář PowerShell |
| probíhá protokol |Určuje, zda mají být pro soubor Runbook generovány [záznamy průběhu.](../../automation/automation-runbook-output-and-messages.md) |
| logVerbose |Určuje, zda mají být pro soubor runbook generovány [podrobné záznamy.](../../automation/automation-runbook-output-and-messages.md) |
| description |Volitelný popis pro runbook. |
| publishContentLink |Určuje obsah runbooku. <br><br>uri - Uri k obsahu runbooku.  Bude to soubor PS1 pro sady Runbook a Sady Runbook aplikace PowerShell a Script a exportovaný grafický soubor sady Runbook pro runbook graph.  <br> verze - Verze runbooku pro vlastní sledování. |


## <a name="automation-jobs"></a>Pracovní chod automatizace
Když spustíte runbook v Azure Automation, vytvoří úlohu automatizace.  Do vašeho řešení můžete přidat prostředek úlohy automatizace a automaticky spustit runbook při instalaci řešení pro správu.  Tato metoda se obvykle používá ke spuštění sady Runbook, které se používají pro počáteční konfiguraci řešení.  Chcete-li spustit runbook v pravidelných intervalech, vytvořte [plán](#schedules) a [plán úloh.](#job-schedules)

Pracovní prostředky mají typ **Microsoft.Automation/automationAccounts/jobs** a mají následující strukturu.  To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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

Vlastnosti úloh automatizace jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| sada Runbook |Entita s jedním názvem s názvem runbooku, který má být spuštěn. |
| parameters |Entita pro každou hodnotu parametru vyžadožoje runbook. |

Úloha obsahuje název runbooku a všechny hodnoty parametrů, které mají být odeslány do runbooku.  Úloha by měla [záviset na]( solutions-solution-file.md#resources) runbooku, který se spouští, protože runbook musí být vytvořen před úlohou.  Pokud máte více runbooků, které by měly být spuštěny, můžete definovat jejich pořadí tím, že úloha závisí na jiných úlohách, které by měly být spuštěny jako první.

Název zdroje úlohy musí obsahovat identifikátor GUID, který je obvykle přiřazen parametrem.  Další informace o parametrech GUID najdete v [části Vytvoření souboru řešení pro správu v Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikáty
[Certifikáty Azure Automation](../../automation/automation-certificates.md) mají typ **Microsoft.Automation/automationAccounts/certificates** a mají následující strukturu. To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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



Vlastnosti prostředků certifikátů jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| base64Hodnota |Základní hodnota 64 certifikátu. |
| Miniatura |Kryptografický otisk certifikátu. |



## <a name="credentials"></a>Přihlašovací údaje
[Pověření Azure Automation](../../automation/automation-credentials.md) mají typ **Microsoft.Automation/automationAccounts/credentials** a mají následující strukturu.  To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 


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

Vlastnosti prostředků pověření jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| userName |Uživatelské jméno pověření. |
| heslo |Heslo pro pověření. |


## <a name="schedules"></a>Plány
[Plány Azure Automation](../../automation/automation-schedules.md) mají typ **Microsoft.Automation/automationAccounts/schedules** a mají následující strukturu. To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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

Vlastnosti zdrojů plánu jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| description |Volitelný popis plánu. |
| startTime |Určuje čas zahájení plánu jako objekt DateTime. Řetězec může být poskytnuta, pokud jej lze převést na platný DateTime. |
| Isenabled |Určuje, zda je plán povolen. |
| interval |Typ intervalu pro plán.<br><br>day<br>hour |
| frequency |Frekvence, že plán by měl oheň v počtu dní nebo hodin. |

Plány musí mít čas zahájení s hodnotou větší než aktuální čas.  Tuto hodnotu nelze zadat s proměnnou, protože byste neměli žádný způsob, jak zjistit, kdy bude nainstalována.

Při použití zdrojů plánu v řešení použijte jednu z následujících dvou strategií.

- Použijte parametr pro počáteční čas plánu.  To vyzve uživatele k zadání hodnoty při instalaci řešení.  Pokud máte více plánů, můžete použít jednu hodnotu parametru pro více než jeden z nich.
- Vytvořte plány pomocí runbooku, který se spustí při instalaci řešení.  Tím odeberete požadavek uživatele zadat čas, ale nelze obsahovat plán v řešení, takže bude odebrán při odebrání řešení.


### <a name="job-schedules"></a>Plány úlohy
Zdroje plánu úloh propojují soubor Runbook s plánem.  Mají typ **Microsoft.Automation/automationAccounts/jobSchedules** a mají následující strukturu.  To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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


Vlastnosti plánů úloh jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| název plánu |Entita s jedním **názvem** s názvem plánu. |
| název souboru runbook  |Entita s jedním **názvem** s názvem runbooku.  |



## <a name="variables"></a>Proměnné
[Proměnné Azure Automation](../../automation/automation-variables.md) mají typ **Microsoft.Automation/automationAccounts/variables** a mají následující strukturu.  To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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

Vlastnosti proměnných zdrojů jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| description | Volitelný popis proměnné. |
| jeŠifrovaný | Určuje, zda má být proměnná zašifrována. |
| type | Tato vlastnost nemá aktuálně žádný vliv.  Datový typ proměnné bude určen počáteční hodnotou. |
| value | Hodnota proměnné. |

> [!NOTE]
> Vlastnost **type** nemá aktuálně žádný vliv na vytvářenou proměnnou.  Datový typ proměnné bude určen hodnotou.  

Pokud nastavíte počáteční hodnotu proměnné, musí být nakonfigurována jako správný datový typ.  Následující tabulka obsahuje různé datové typy povolené a jejich syntaxi.  Všimněte si, že hodnoty v JSON se očekává, že vždy uzavřeny v uvozovkách s žádné speciální znaky v uvozovkách.  Například hodnota řetězce by byla určena uvozovkami kolem\\řetězce (pomocí řídicího znaku ( )), zatímco číselná hodnota by byla zadána s jednou sadou uvozovek.

| Datový typ | Popis | Příklad | Řeší, že |
|:--|:--|:--|:--|
| řetězec   | Uzavřete hodnotu do dvojitých uvozovek.  | "\"Hello\"world " | "Dobrý svět" |
| numerické  | Číselná hodnota s jednoduchými uvozovkami.| "64" | 64 |
| Boolean  | **pravdivé** nebo **nepravdivé** v uvozovkách.  Všimněte si, že tato hodnota musí být malá písmena. | "pravda" | true |
| datetime | Serializovaná hodnota data.<br>Rutina ConvertTo-Json v prostředí PowerShell můžete použít ke generování této hodnoty pro určité datum.<br>Příklad: datum získání "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Datum(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduly
Vaše řešení pro správu nemusí definovat globální moduly používané vašimi [runbooky,](../../automation/automation-integration-modules.md) protože budou vždy k dispozici ve vašem účtu Automation.  Je třeba zahrnout prostředek pro jakýkoli jiný modul používaný sady Runbook.

[Integrační moduly](../../automation/automation-integration-modules.md) mají typ **Microsoft.Automation/automationAccounts/modules** a mají následující strukturu.  To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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


Vlastnosti prostředků modulu jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Contentlink |Určuje obsah modulu. <br><br>uri - Uri k obsahu modulu.  Bude to soubor PS1 pro sady Runbook a Sady Runbook aplikace PowerShell a Script a exportovaný grafický soubor sady Runbook pro runbook graph.  <br> verze - Verze modulu pro vlastní sledování. |

Runbook by měl záviset na prostředku modulu, aby bylo zajištěno, že je vytvořen před runbook.

### <a name="updating-modules"></a>Aktualizace modulů
Pokud aktualizujete řešení pro správu, které obsahuje runbook, který používá plán, a nová verze vašeho řešení má nový modul používaný tímto runbookem, pak může runbook používat starou verzi modulu.  Do řešení byste měli zahrnout následující runbooky a vytvořit úlohu pro jejich spuštění před jakoukoli jinou sadou Runbook.  Tím zajistíte, že všechny moduly jsou aktualizovány podle potřeby před načtením sady Runbook.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) zajistí, že všechny moduly používané runbooky ve vašem řešení jsou nejnovější verze.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) znovu zaregistruje všechny prostředky plánu, aby bylo zajištěno, že sady Runbook s nimi propojené pomocí nejnovějších modulů.




## <a name="sample"></a>Ukázka
Následuje ukázka řešení, které zahrnuje následující zdroje:

- Runbook.  Toto je ukázkový runbook uložený ve veřejném úložišti GitHub.
- Úloha automatizace, která spustí runbook při instalaci řešení.
- Plán a plán úloh pro spuštění runbooku v pravidelných intervalech.
- Certifikát.
- Pověření.
- Proměnné.
- Modul.  Toto je [modul OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) pro zápis dat do log analytics. 

Ukázka používá standardní [parametry řešení]( solutions-solution-file.md#parameters) proměnné, které by se běžně používají v řešení na rozdíl od hardcoding hodnoty v definicích prostředků.


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
* [Přidejte zobrazení do vašeho řešení]( solutions-resources-views.md) a vizualizovat shromážděná data.
