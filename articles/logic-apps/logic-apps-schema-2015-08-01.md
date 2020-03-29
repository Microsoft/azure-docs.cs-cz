---
title: Aktualizace schématu pro náhled srpen-1-2015
description: Aktualizované schéma verze 2015-08-01-preview pro definice aplikací logiky v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792838"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizace schématu pro Aplikace Logika Azure – 1.

Toto schéma a verze rozhraní API pro Azure Logic Apps obsahuje klíčová vylepšení, která usnadňují a snadněji se používají aplikace logiky:

* Typ akce **APIApp** se nyní nazývá [**APIConnection**](#api-connections).
* Akce **Opakování** se nyní nazývá [**Foreach**](#foreach).
* Aplikace rozhraní API [ **naslouchací proces HTTP** ](#http-listener) již není vyžadována.
* Volání podřízených pracovních postupů používá [nové schéma](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Přechod na připojení rozhraní API

Největší změnou je, že už nemusíte nasazovat aplikace rozhraní API do předplatného Azure, abyste mohli používat rozhraní API. Tady jsou způsoby, jak můžete použít api:

* Spravovaná přístupová avis
* Vlastní webová api

Každý způsob je zpracován mírně odlišně, protože jejich modely správy a hostování jsou různé. Jednou z výhod tohoto modelu je, že už nejste omezeni na prostředky, které jsou nasazené ve vaší skupině prostředků Azure. 

### <a name="managed-apis"></a>Spravovaná přístupová avis

Společnost Microsoft spravuje některá rozhraní API vaším jménem, například Office 365, Salesforce, Twitter a FTP. Některá spravovaná api můžete použít tak, jak jsou, například Překladač Bing, zatímco jiná vyžadují konfiguraci, která se také nazývá *připojení*.

Když například používáte Office 365, musíte vytvořit připojení, které obsahuje váš přihlašovací token Office 365. Váš token je bezpečně uložena a aktualizována tak, aby vaše aplikace logiky můžete vždy volat rozhraní API Office 365. Pokud se chcete připojit k serveru SQL nebo FTP, musíte vytvořit připojení, které má připojovací řetězec. 

V této definici se `APIConnection`tyto akce nazývají . Tady je příklad připojení, které volá Office 365 a posílá e-mail:

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

Objekt `host` je součástí vstupů, které jsou jedinečné pro připojení rozhraní `api` `connection`API a obsahuje tyto části: a . Objekt `api` určuje adresu URL za běhu, kde je spravované rozhraní API hostováno. Můžete zobrazit všechny dostupné spravované rozhraní API voláním této metody:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Při použití rozhraní API, toto rozhraní API může nebo nemusí mít definované žádné *parametry připojení*. Takže pokud rozhraní API nedefinuje tyto parametry, není vyžadováno žádné připojení. Pokud rozhraní API definuje tyto parametry, musíte vytvořit připojení se zadaným názvem.  
Potom odkaz na tento `connection` název `host` v objektu uvnitř objektu. Chcete-li vytvořit připojení ve skupině prostředků, zavolejte tuto metodu:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

S následujícím tělem:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Nasazení spravovaných api v šabloně Azure Resource Manageru

Pokud interaktivní přihlášení není vyžadováno, můžete vytvořit úplnou aplikaci pomocí šablony Správce prostředků.
Pokud je vyžadováno přihlášení, můžete dál používat šablonu Správce prostředků, ale musíte autorizovat připojení prostřednictvím portálu Azure. 

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

V tomto příkladu se zobrazí, že připojení jsou pouze prostředky, které žijí ve vaší skupině prostředků. Odkazují na spravovaná rozhraní API, která máte k dispozici ve vašem předplatném.

### <a name="your-custom-web-apis"></a>Vlastní webová api

Pokud používáte vlastní rozhraní API, nikoli rozhraní spravovaná společností Microsoft, použijte integrovanou akci **HTTP** k volání rozhraní API. V ideálním případě byste měli poskytnout koncový bod Swagger pro vaše rozhraní API. Tento koncový bod pomáhá Návrháři aplikací logiky zobrazit vstupy a výstupy rozhraní API. Bez koncový bod Swagger návrháře můžete zobrazit pouze vstupy a výstupy jako neprůhledné objekty JSON.

Zde je příklad ukazující `metadata.apiDefinitionUrl` novou vlastnost:

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

Pokud hostujete webové rozhraní API ve službě Azure App Service, vaše webové rozhraní API se automaticky zobrazí v seznamu akcí dostupných v návrháři. Pokud ne, musíte vložit do adresy URL přímo. Koncový bod Swagger musí být neověřené, aby byly použitelné v návrháři aplikace logiky, i když můžete zabezpečit samotné rozhraní API s jakékoli metody, které podporuje Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Volání nasazených aplikací rozhraní API s 2015-08-01-preview

Pokud jste dříve nasadili aplikaci rozhraní API, můžete tuto aplikaci volat pomocí akce **HTTP.**
Pokud například používáte Dropbox k vyseznamovat soubory, může mít definice verze schématu **2014-12-01-preview** něco jako:

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

Nyní můžete vytvořit podobnou akci HTTP a ponechat `parameters` část definice aplikace logiky beze změny, například:

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

Procházení těchto vlastností jeden po druhém:

| Vlastnost akce | Popis |
| --- | --- |
| `type` | `Http`Namísto`APIapp` |
| `metadata.apiDefinitionUrl` | Chcete-li tuto akci použít v Návrháři aplikace logiky, zahrňte koncový bod metadat, který je vytvořen z:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Vyrobeno z:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Vždy`POST` |
| `inputs.body` | Stejné jako parametry aplikace API |
| `inputs.authentication` | Stejné jako ověřování aplikace API |

Tento přístup by měl fungovat pro všechny akce aplikace rozhraní API. Nezapomeňte však, že tyto předchozí aplikace rozhraní API již nejsou podporovány. Takže byste měli přejít na jednu ze dvou dalších předchozích možností, spravované rozhraní API nebo hostování vlastního webového rozhraní API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Přejmenováno na "repeat" na 'foreach'

Pro předchozí verzi schématu jsme obdrželi mnoho zpětné vazby od zákazníků, že název akce **Opakování** byl matoucí a nezachytil správně, že **opakování** bylo opravdu pro každou smyčku. Takže jsme přejmenovali `repeat` na `foreach`. Dříve byste tuto akci napsali takto:

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

Nyní byste napsat tuto verzi místo:

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

`repeatItem()` Funkce, která odkazuje na položku, kterou smyčka zpracovává během aktuální iterace, je nyní přejmenována `item()`. 

### <a name="reference-outputs-from-foreach"></a>Referenční výstupy z 'foreach'

Pro zjednodušení výstupy `foreach` z akcí již nejsou zabaleny `repeatItems`do objektu s názvem . S těmito změnami `repeatItem()` `repeatBody()`jsou `repeatOutputs()` také odebrány funkce , a funkce.

Takže pomocí předchozího `repeat` příkladu získáte tyto výstupy:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Nyní získáte tyto výstupy místo:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
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
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Nyní můžete použít tuto verzi místo:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Nativní naslouchací proces HTTP

Funkce naslouchací proces HTTP jsou nyní integrované, takže není třeba nasadit aplikaci http listener API. Další informace najdete v tom, jak [zefektivnit koncový bod aplikace logiky](../logic-apps/logic-apps-http-endpoint.md). 

S těmito změnami Logic `@accessKeys()` Apps `@listCallbackURL()` nahradí funkci s funkcí, která získá koncový bod v případě potřeby. Také nyní musíte definovat alespoň jednu aktivační událost v aplikaci logiky. Chcete-li `/run` pracovní postup, musíte použít jeden z `Manual`těchto `ApiConnectionWebhook`typů aktivačních událostí: , , nebo`HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Volání podřízených pracovních postupů

Dříve volání podřízené pracovní postupy nutné přejít na pracovní postup, získání přístupového tokenu a vložení tokenu v definici aplikace logiky, kde chcete volat, že podřízený pracovní postup. Pomocí tohoto schématu modul Logic Apps automaticky generuje SAS za běhu pro podřízený pracovní postup, takže není třeba vkládat žádné tajné kódy do definice. Zde naleznete příklad:

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

Podřízené pracovní postupy také získat úplný přístup k příchozí žádosti. Takže můžete předat parametry `queries` v sekci `headers` a v objektu. Můžete také plně definovat `body` celý oddíl.

Nakonec podřízené pracovní postupy mají tyto požadované změny. Zatímco dříve a přímo můžete volat podřízený pracovní postup, musíte nyní definovat koncový bod aktivační události v pracovním postupu pro nadřazený volat. Obecně byste přidat aktivační událost, která má `Manual` typ a pak použít aktivační událost v nadřazené definice. Vlastnost `host` má konkrétně `triggerName` protože je nutné vždy zadat aktivační událost, kterou voláte.

## <a name="other-changes"></a>Další změny

### <a name="new-queries-property"></a>Nová vlastnost "dotazy"

Všechny typy akcí nyní podporují `queries`nový vstup s názvem . Tento vstup může být strukturovaný objekt, spíše než budete muset sestavit řetězec ručně.

### <a name="renamed-parse-function-to-json"></a>Přejmenována na funkci "parse()' na 'json()'

Funkce `parse()` je nyní přejmenována na `json()` funkci pro budoucí typy obsahu.

## <a name="enterprise-integration-apis"></a>Přístupová api pro podnikovou integraci

Toto schéma ještě nepodporuje spravované verze pro is enterprise integration API, jako je například AS2. Můžete však použít existující nasazená BizTalk API prostřednictvím akce HTTP. Další informace naleznete v tématu "Using your already deployed API apps" v [plánu integrace](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
