---
title: Aktualizace schématu pro 1. srpna 2015 preview – Azure Logic Apps | Dokumentace Microsoftu
description: Aktualizované schéma verze 2015-08-01-preview pro definic aplikací logiky v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: ec6f98ca0f0260a0d7bed16538f557931cd2e33e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080006"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizace schématu pro Azure Logic Apps - 1. srpna 2015 preview

Toto schéma a rozhraní API verze pro Azure Logic Apps zahrnuje klíčových vylepšení, které spolehlivější a usnadňuje používání logic apps:

* **APIApp** typ akce je teď jmenuje [ **APIConnection**](#api-connections).
* **Opakujte** akce je teď jmenuje [ **Foreach**](#foreach).
* [ **Naslouchací proces protokolu HTTP** aplikace API](#http-listener) se už nevyžaduje.
* Používá volání podřízených pracovních postupů [nové schéma](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Přesunout připojení rozhraní API

Největší změnou je, že již nemusíte nasazovat aplikace API do vašeho předplatného Azure, aby mohli používat rozhraní API. Tady je způsoby, které můžete použít rozhraní API:

* Spravované rozhraní API
* Vlastní webová rozhraní API.

Jednotlivé možnosti přinesou je zpracovávané trochu jinak, protože se liší jejich správa a hostování modely. Jednou z výhod tohoto modelu je, že jste se k prostředkům, které jsou nasazené ve vaší skupině prostředků Azure již nejsou omezené. 

### <a name="managed-apis"></a>Spravované rozhraní API

Některá rozhraní API za vás, jako je Office 365, Salesforce, Twitter a FTP spravuje Microsoft. Můžete použít některá spravovaná rozhraní API jako-, jako je Bing přeložit, zatímco jiné vyžadují konfiguraci, zkratka *připojení*.

Například pokud používáte Office 365, musí vytvořit připojení, která zahrnuje Office 365 přihlašovací token. Váš token je bezpečně uložená a aktualizovat tak, aby vaše aplikace logiky můžete vždy volat rozhraní API Office 365. Pokud se chcete připojit k serveru SQL nebo FTP, musíte vytvořit připojení, které obsahuje připojovací řetězec. 

V této definici, se nazývají tyto akce `APIConnection`. Tady je příklad připojení, která volá Office 365 k odesílání e-mailu:

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

`host` Objektu je součástí vstupy, které jsou jedinečné pro připojení rozhraní API a obsahuje tyto části: `api` a `connection`. `api` Objekt určuje modulu runtime prostředí adresu URL, kde spravované rozhraní API. Voláním této metody můžete zobrazit všechny dostupné spravované API:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Při použití rozhraní API, toto rozhraní API může nebo nemusí mít definované žádné *parametry připojení*. Takže pokud rozhraní API nedefinuje tyto parametry, je nutné žádné připojení. Pokud rozhraní API definovat tyto parametry, musíte vytvořit spojení se zadaným názvem.  
Pak odkazovat tento název v `connection` objektu `host` objektu. Chcete-li vytvořit připojení ve skupině prostředků, volání této metody:

```text
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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Nasazení spravovaných rozhraní API v šabloně Azure Resource Manageru

Při interaktivním přihlášení se vyžaduje, můžete vytvořit úplnou aplikaci pomocí šablony Resource Manageru.
Pokud přihlášení je potřeba, můžete stále použít šablonu Resource Manageru, ale budete muset autorizovat připojení na webu Azure portal. 

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

Zobrazí se v tomto příkladu, že připojení jsou jenom prostředky, které za provozu ve vaší skupině prostředků. Odkazují spravovaných rozhraní API k dispozici v rámci vašeho předplatného.

### <a name="your-custom-web-apis"></a>Vlastní webová rozhraní API.

Pokud používáte vlastní rozhraní API než ty spravovaných microsoftem, použít integrovaný **HTTP** akce pro volání rozhraní API. V ideálním případě byste měli poskytnout koncového bodu Swaggeru pro vaše rozhraní API. Tento koncový bod pomáhá návrhář aplikace logiky zobrazit vstupy a výstupy vašeho rozhraní API. Bez koncového bodu Swaggeru návrháře můžete jenom zobrazit vstupy a výstupy jako neprůhledný objekty JSON.

Tady je příklad ukazující nový `metadata.apiDefinitionUrl` vlastnost:

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

Pokud hostujete vaše webové rozhraní API ve službě Azure App Service, vaše webové rozhraní API se automaticky zobrazí v seznamu akcí, které jsou k dispozici v návrháři. V opačném případě budete muset vložit přímo v adrese URL. Koncový bod Swaggeru musí neověřené má být použitelná v návrháři aplikace logiky, i když můžete zabezpečit samotné rozhraní API pomocí libovolné metody, které podporuje Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Volání nasazené rozhraní API aplikací s 2015-08-01-preview

Pokud jste předtím nasadili aplikaci API, můžete volat tuto aplikaci se **HTTP** akce.
Pokud používáte Dropboxu do seznamu souborů, například vaše **2014-12-01-preview** definici verze schématu může mít vypadat:

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

Nyní, teď můžete sestavit podobné akce HTTP a nechte definici aplikace logiky `parameters` beze změny části, například:

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

Provede jednu po druhé tyto vlastnosti:

| Vlastnosti akce | Popis |
| --- | --- |
| `type` | `Http` Namísto `APIapp` |
| `metadata.apiDefinitionUrl` | Pokud chcete použít tuto akci v návrháři aplikace logiky, patří koncový bod metadat, která je vytvořená z: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Vytvořený z: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Vždy `POST` |
| `inputs.body` | Stejné jako parametry aplikace API |
| `inputs.authentication` | Stejné jako ověření aplikace API |

Tento přístup by měla fungovat pro všechny akce aplikace API. Nezapomeňte však, že tato předchozí API Apps již nejsou podporovány. Proto byste měli přejít na dvou dalších předchozí možnosti, spravované rozhraní API nebo hostování vlastního rozhraní Web API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Přejmenovat 'opakovat' na 'foreach.

Pro předchozí verzi schématu, dostali jsme spoustu zpětné vazby od zákazníků, který **opakujte** název akce byl nepřehledný a neměli zachycení správně, který **opakujte** znamenalo smyčky for-each. Proto jsme přejmenovali `repeat` k `foreach`. Dříve měli byste napsat tuto akci, jako v tomto příkladu:

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

Nyní byste napsat tuto verzi místo toho:

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

Také `repeatItem()` funkce, který odkazuje položka, která zpracovává během aktuální iteraci smyčky, přejmenovává `item()`. 

### <a name="reference-outputs-from-foreach"></a>Odkaz na výstupy z "foreach.

Pro zjednodušení, výstupy z `foreach` akce jsou už zabalené v objektu s názvem `repeatItems`. Navíc se tyto změny `repeatItem()`, `repeatBody()`, a `repeatOutputs()` funkce jsou odebrány.

Ano, pomocí předchozího `repeat` například získat tyto výstupy:

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

Teď získáte tyto výstupy místo:

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

Dříve Chcete-li získat `body` z akce při odkazování na tyto výstupy:

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

Tato verze teď můžete použít místo toho:

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

Funkce naslouchací proces protokolu HTTP jsou teď integrované, takže nebudete muset nasazovat aplikace API naslouchací proces protokolu HTTP. Další informace, přečtěte si postup [vytvořit váš koncový bod aplikace logiky volatelných](../logic-apps/logic-apps-http-endpoint.md). 

S těmito změnami se nahradí Logic Apps `@accessKeys()` pracovat `@listCallbackURL()` funkce, která získá koncový bod, pokud je to nezbytné. Navíc teď musíte definovat alespoň jednu aktivační událost ve své aplikaci logiky. Pokud chcete `/run` pracovního postupu, je nutné použít jeden z těchto typů aktivační události: `Manual`, `ApiConnectionWebhook`, nebo `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Volat podřízené pracovních postupů

Volání podřízených pracovních postupů vyžaduje dříve, že přejdete do pracovního postupu, získání přístupového tokenu a vložení tokenu v definici aplikace logiky, ve kterém chcete volat tuto podřízený pracovní postup. Pomocí tohoto schématu modul Logic Apps automaticky vygeneruje sdílený přístupový podpis za běhu pro podřízený pracovní postup, není potřeba vkládat jakýchkoli tajných kódů do v definici. Zde naleznete příklad:

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

Podřízené pracovní postupy získáte také úplný přístup k příchozího požadavku. Ano, můžete předávat parametry v `queries` části a v `headers` objektu. Můžete také plně definovat celou `body` oddílu.

Nakonec podřízených pracovních postupů jsou tyto požadované změny. I když může dříve a volat přímo podřízený pracovní postup, je musí definovat koncový bod aktivační událost v pracovním postupu pro nadřazenou položku k volání. Obecně platí, přidáte trigger, který se má `Manual` zadejte a pak pomocí této aktivační události v definici nadřazené. `host` Konkrétně má vlastnost `triggerName` protože vždy je nutné zadat aktivační událost voláte.

## <a name="other-changes"></a>Další změny

### <a name="new-queries-property"></a>Nové vlastnosti 'dotazy.

Všechny typy akcí teď podporují nové vstupu volá `queries`. Tento vstup může být strukturovaný objekt, spíše než by bylo nutné sestavit řetězec ručně.

### <a name="renamed-parse-function-to-json"></a>Přejmenované "parse()" funkce "json().

`parse()` Funkce přejmenovává `json()` funkce pro budoucí typy obsahu.

## <a name="enterprise-integration-apis"></a>Podnikové integrace rozhraní API

Toto schéma zatím nepodporuje spravované verze pro podnikové integrace rozhraní API, jako je například AS2. Můžete však použít existující nasazené rozhraním API BizTalku prostřednictvím akce HTTP. Další informace najdete v tématu "Pomocí aplikace již nasazené rozhraní API" v [integrace plán](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
