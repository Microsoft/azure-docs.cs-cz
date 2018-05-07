---
title: Schéma se aktualizuje srpen-1-2015 preview – Azure Logic Apps | Microsoft Docs
description: Vytvoření definice JSON pro Azure Logic Apps pomocí schématu verze 2015-08-01-preview
author: stepsic-microsoft-com
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: bdadc2e33082421500f21d5926ac1e660f4164d4
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizace schématu pro Azure Logic Apps - 1. srpna 2015 preview

Toto schéma a rozhraní API verze pro Azure Logic Apps zahrnuje klíčových vylepšení, které aplikace logiky spolehlivější a jednodušší použít:

* **APIApp** typ akce teď jmenuje [ **APIConnection**](#api-connections).
* **Opakovat** akce teď jmenuje [ **Foreach**](#foreach).
* [ **Naslouchací proces protokolu HTTP** aplikace API](#http-listener) už nepotřebujete.
* Volání metody podřízené pracovní postupy používá [nové schéma](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Přesunout do rozhraní API připojení

Největší změnou je, že už máte k nasazení aplikace API do předplatného Azure tak, aby můžete použít rozhraní API. Zde jsou způsoby, které můžete použít rozhraní API:

* Spravovaná rozhraní API
* Vlastní webová rozhraní API.

Každý způsob je zpracovávané trochu jinak, protože jejich správy a hostování modely se liší. Jednou z výhod tohoto modelu je, že jste se k prostředkům, které jsou nasazeny ve vaší skupině prostředků Azure už omezená. 

### <a name="managed-apis"></a>Spravovaná rozhraní API

Microsoft spravuje některé rozhraní API vaším jménem, jako je například Office 365, Salesforce, Twitter a FTP. Můžete použít některé spravovaná rozhraní API jako-, jako je například Bing přeložit, zatímco jiné vyžadují konfiguraci, zkratka *připojení*.

Například pokud používáte Office 365, musí vytvořit připojení, které zahrnuje přihlašovací token Office 365. Váš token je bezpečně uložená a aktualizovat tak, aby aplikace logiky můžete vždy volat rozhraní API Office 365. Pokud se chcete připojit k serveru SQL nebo FTP, musíte vytvořit připojení, který má připojovací řetězec. 

V této definici, se nazývají tyto akce `APIConnection`. Tady je příklad připojení, která volá Office 365 odeslat e-mail:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

`host` Objektu je část vstupních hodnot, která je jedinečná připojení rozhraní API a obsahuje tyto části: `api` a `connection`. `api` Objektu určuje runtime je adresa URL, pro které který spravovaná rozhraní API hostovaná. Zobrazí všechny dostupné spravované rozhraní API voláním `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Pokud používáte rozhraní API, toto rozhraní API může nebo nemusí mít definovaný žádný *parametrů připojení*. Takže pokud rozhraní API není definována. Tyto parametry, je nutné žádné připojení. Pokud rozhraní API definovat tyto parametry, musíte vytvořit spojení se zadaným názvem.  
Pak odkazovat tento název v `connection` objektu uvnitř `host` objektu. Pokud chcete vytvořit připojení ve skupině prostředků, volání této metody:

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Spolu s následujícím textem:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Nasadit spravované rozhraní API v šablonu Azure Resource Manager

Můžete vytvořit úplné aplikaci v šablonu Azure Resource Manager, tak dlouho, dokud interaktivní přihlašování není povinné.
Pokud přihlášení je potřeba, můžete nastavit všechno pomocí šablony Azure Resource Manager, ale je třeba navštívit portál Azure k autorizaci připojení. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

Zobrazí se v tomto příkladu, že připojení jsou jenom prostředky, které za provozu ve vaší skupině prostředků. Jejich referenční spravovaná rozhraní API k dispozici v rámci vašeho předplatného.

### <a name="your-custom-web-apis"></a>Vlastní webová rozhraní API.

Pokud používáte vlastní rozhraní API, není spravovaný společností Microsoft ty použít integrované **HTTP** akce je volat. Pro ideální prostředí by měl vystavit koncový bod Swagger pro vaše rozhraní API. Tento koncový bod umožňuje návrháře logiku aplikace k vykreslení vstupy a výstupy pro vaše rozhraní API. Bez Swagger návrháře lze zobrazit pouze vstupy a výstupy jako neprůhledné objekty JSON.

Tady je příklad zobrazuje nové `metadata.apiDefinitionUrl` vlastnost:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Pokud hostujete webové rozhraní API v Azure App Service, Web API se automaticky zobrazí v seznamu akcí, které jsou k dispozici v návrháři. Pokud ne, je nutné vložit v adrese URL přímo. Koncový bod Swagger musí neověřené možné používat v návrháři aplikace logiky, i když můžete zabezpečit samotné rozhraní API pomocí jakékoli metody, které podporuje Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Volání nasazené aplikace API s 2015-08-01-preview

Pokud jste nasadili aplikace API, obraťte se na tuto aplikaci s **HTTP** akce.
Pokud používáte Dropbox seznam souborů, například vaše **2014-12-01-preview** definice schématu verze může mít něco podobného jako:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Při opuštění sekci parametrů pro danou definici aplikace logiky beze změny teď můžete vytvořit nyní ekvivalentní akci HTTP jako v následujícím příkladu:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Tyto vlastnosti po jednom s návodem:

| Vlastnosti akce. | Popis |
| --- | --- |
| `type` | `Http` Namísto `APIapp` |
| `metadata.apiDefinitionUrl` | Pokud chcete používat tuto akci v návrháři aplikace logiky, patří koncový bod metadat, které se vytvářejí na základě: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Vytvářejí na základě: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Vždy `POST` |
| `inputs.body` | Stejný jako parametry aplikace API |
| `inputs.authentication` | Shodné s ověřováním aplikace API |

Tento postup by měly fungovat pro všechny akce aplikace API. Nezapomeňte však, že tato předchozí aplikace API již nejsou podporovány. Proto byste měli přesunout do jednoho z následujících dvou dalších předchozích možností, spravované rozhraní API nebo hostující vaše vlastní webové rozhraní API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Přejmenovat 'opakovat' do 'foreach.

Pro předchozí verze schématu dostali jsme mnohem názory zákazníků, **opakovat** název akce bylo matoucí a nebyla zachycení správně, **opakujte** je skutečně pro každou smyčku. Ano, jsme přejmenovat `repeat` k `foreach`. Dříve by tato akce jako tento ukázkový zápisu:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Teď by zapsat tuto verzi místo:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Navíc `repeatItem()` funkce, které odkazovalo na položku, která zpracovává během na aktuální iteraci smyčky, je nyní přejmenovat `item()`. 

### <a name="reference-outputs-from-foreach"></a>Referenční dokumentace výstupy z 'foreach.

Pro zjednodušení, výstupy z `foreach` akce jsou již uzavřen do objekt s názvem `repeatItems`. Navíc se tyto změny `repeatItem()`, `repeatBody()`, a `repeatOutputs()` funkce jsou odebrány.

Ano, použití předchozí `repeat` například získat tyto výstupy:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Teď můžete získat tyto výstupy místo:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Dříve získat `body` z akce při odkazování na tyto výstupy:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Teď můžete použít tuto verzi místo:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Nativní naslouchací proces protokolu HTTP

Možnosti naslouchací proces protokolu HTTP jsou teď součástí. Proto již nepotřebujete k nasazení aplikace API naslouchací proces protokolu HTTP. V tématu [úplné podrobnosti o tom váš koncový bod aplikace logiky s zde](../logic-apps/logic-apps-http-endpoint.md). 

Tyto změny, jsme odebrali `@accessKeys()` funkce, které bylo nahrazeno `@listCallbackURL()` funkce pro získání koncového bodu, pokud je to nezbytné. Navíc je nutné nyní zadat nejméně jedna aktivační událost v aplikaci logiky. Pokud chcete `/run` pracovní postup, musí mít jednu z těchto aktivační události: `manual`, `apiConnectionWebhook`, nebo `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Volat podřízené pracovní postupy

Volání podřízených pracovních požadované dříve, přejdete do pracovního postupu, získání tokenu přístupu a vkládání token v definici aplikace logiky, kde chcete volat podřízené postupu. S tímto schématem modul Logic Apps automaticky vygeneruje SAS v době běhu pro podřízené pracovní postup, nemusíte vložte všech tajných klíčů do definice. Zde naleznete příklad:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Druhý zlepšování je že nabízíme podřízených pracovních úplný přístup k příchozího požadavku. To znamená, že můžete předat parametry v *dotazy* části a v *hlavičky* objekt a že můžete definovat plně celého obsahu.

Nakonec nejsou požadované změny pro podřízený pracovní postup. Při podřízený pracovní postup může volat dříve přímo, nyní je nutné definovat koncový bod aktivační událost v pracovním postupu pro nadřazený prvek volat. Obecně platí, měli byste přidat aktivační událost, která má `manual` zadejte a potom pomocí této aktivační události v nadřazené definici. Poznámka: `host` vlastnost konkrétně má `triggerName` vzhledem k tomu, že musíte určit které aktivační událost je vyvolán.

## <a name="other-changes"></a>Další změny

### <a name="new-queries-property"></a>Nové vlastnosti 'dotazy.

Všechny typy akcí teď podporují nové vstup názvem `queries`. Tento vstup může být strukturovaný objekt, místo nutnosti ke kompilaci řetězec ručně.

### <a name="renamed-parse-function-to-json"></a>Funkce přejmenován 'parse()' 'json().

Přidáváme další obsah typy brzy, takže jsme přejmenován `parse()` funkci, aby se `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Připravuje se: Enterprise integrace rozhraní API

Nemáme spravované verze ještě Enterprise integrace rozhraní API, jako je AS2. Mezitím můžete vaší existující nasazené API BizTalk pomocí akce HTTP. Podrobnosti najdete v tématu "Použití již nasazené aplikace API" v [integrace plán](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
