---
title: Aktualizace schématu pro srpen-1-2015 Preview
description: Aktualizované schéma verze 2015-08-01-Preview pro definice aplikací logiky v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792838"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Aktualizace schématu pro Azure Logic Apps – 1. srpna 2015 Preview

Toto schéma a verze rozhraní API pro Azure Logic Apps obsahují klíčová vylepšení, která umožňují spolehlivější a snazší používání Logic Apps:

* Typ akce **vstupech apiapp** má nyní název [**vstupech apiconnection**](#api-connections).
* Akce **Opakovat** je nyní pojmenována [**foreach**](#foreach).
* [Aplikace API **naslouchacího procesu http** ](#http-listener) už není potřeba.
* Volání podřízených pracovních postupů používá [nové schéma](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Přesunout na připojení rozhraní API

Největší změnou je, že už nemusíte nasazovat API Apps do předplatného Azure, abyste mohli používat rozhraní API. Tady je způsob, jak můžete používat rozhraní API:

* Spravovaná rozhraní API
* Vaše vlastní webová rozhraní API

Každý způsob je zpracováván mírně jinak, protože jejich modely správy a hostování jsou odlišné. Jednou z výhod tohoto modelu je, že už nejste omezeni na prostředky, které jsou nasazené ve vaší skupině prostředků Azure. 

### <a name="managed-apis"></a>Spravovaná rozhraní API

Společnost Microsoft spravuje některá rozhraní API vaším jménem, například Office 365, Salesforce, Twitter a FTP. Některá spravovaná rozhraní API můžete použít tak, jak jsou, jako je například překlad Bingu, zatímco jiné vyžadují konfiguraci, která se označuje také jako *připojení*.

Pokud například používáte Office 365, musíte vytvořit připojení, které zahrnuje token pro přihlášení k Office 365. Váš token je bezpečně uložený a aktualizovaný, takže aplikace logiky může vždycky volat rozhraní API pro Office 365. Pokud se chcete připojit k serveru SQL nebo FTP, je nutné vytvořit připojení s připojovacím řetězcem. 

V této definici se tyto akce nazývají `APIConnection`. Tady je příklad připojení, které volá Office 365 k odeslání e-mailu:

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

Objekt `host` je součástí vstupů, které jsou jedinečné pro připojení rozhraní API a obsahují tyto části: `api` a `connection`. Objekt `api` Určuje adresu URL modulu runtime, kde je toto spravované rozhraní API hostované. Můžete zobrazit všechna dostupná spravovaná rozhraní API voláním této metody:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Když použijete rozhraní API, toto rozhraní API může nebo nemusí mít definovány žádné *parametry připojení*. Takže pokud rozhraní API tyto parametry nedefinuje, není nutné žádné připojení. Pokud rozhraní API definuje tyto parametry, je nutné vytvořit připojení se zadaným názvem.  
Pak se na tento název odkazuje v objektu `connection` uvnitř objektu `host`. Pokud chcete ve skupině prostředků vytvořit připojení, zavolejte tuto metodu:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

S následujícím textem:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Nasazení spravovaných rozhraní API v šabloně Azure Resource Manager

Pokud se interaktivní přihlášení nevyžaduje, můžete vytvořit úplnou aplikaci pomocí šablony Správce prostředků.
Pokud je vyžadováno přihlášení, můžete přesto použít šablonu Správce prostředků, ale je nutné autorizovat připojení prostřednictvím Azure Portal. 

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

V tomto příkladu vidíte, že připojení jsou pouze prostředky, které jsou ve vaší skupině prostředků živé. Odkazují na spravovaná rozhraní API, která jsou k dispozici v rámci vašeho předplatného.

### <a name="your-custom-web-apis"></a>Vaše vlastní webová rozhraní API

Pokud používáte vlastní rozhraní API namísto spravovaných společností Microsoft, použijte k volání rozhraní API vestavěnou akci **http** . V ideálním případě byste měli pro své rozhraní API zadat koncový bod Swagger. Tento koncový bod pomáhá návrháři aplikace logiky zobrazit vstupy a výstupy vašeho rozhraní API. Bez koncového bodu Swagger může Návrhář zobrazit pouze vstupy a výstupy jako neprůhledné objekty JSON.

Tady je příklad, který ukazuje novou vlastnost `metadata.apiDefinitionUrl`:

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

Pokud vaše webové rozhraní API hostuje na Azure App Service, vaše webové rozhraní API se automaticky zobrazí v seznamu akcí, které jsou k dispozici v návrháři. V takovém případě je nutné přímo vložit adresu URL. Aby bylo možné použít koncový bod Swagger v návrháři aplikace logiky, je nutné, aby bylo možné toto rozhraní API zabezpečit bez ohledu na to, jaké metody podporuje Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Volání nasazených aplikací API s 2015-08-01 – Preview

Pokud jste předtím nasadili aplikaci API, můžete tuto aplikaci zavolat pomocí akce **http** .
Pokud například použijete Dropbox k vypsání souborů, může definice verze schématu **2014-12-01-Preview** vypadat přibližně takto:

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

Nyní teď můžete vytvořit podobnou akci HTTP a nechat nezměněnou sekci `parameters` definice aplikace logiky, například:

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

Procházení těchto vlastností po jednom:

| Action – vlastnost | Popis |
| --- | --- |
| `type` | `Http` místo `APIapp` |
| `metadata.apiDefinitionUrl` | Pokud chcete tuto akci použít v návrháři aplikace logiky, zahrňte koncový bod metadat, který je vytvořený z: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Vyrobeno z: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Vždycky `POST` |
| `inputs.body` | Stejné jako parametry aplikace API |
| `inputs.authentication` | Stejné jako ověřování API App |

Tento přístup by měl fungovat pro všechny akce aplikace API. Nezapomeňte však, že tyto předchozí API Apps již nejsou podporovány. Proto byste měli přejít na jednu z dalších dvou předchozích možností, spravované rozhraní API nebo hostování vlastního webového rozhraní API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Opakovat přejmenování na foreach

U předchozí verze schématu jsme obdrželi spoustu názorů zákazníků, že název akce **opakování** byl matoucí, a nedokázal správně zachytit toto **opakování** ve skutečnosti pro každou smyčku. Proto jsme přejmenovali `repeat` na `foreach`. Dříve jste tuto akci napsali jako v tomto příkladu:

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

Nyní zapíšete tuto verzi:

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

Také funkce `repeatItem()`, která odkazovala na položku, kterou smyčka zpracovává během aktuální iterace, je nyní přejmenována `item()`. 

### <a name="reference-outputs-from-foreach"></a>Výstupy odkazů z příkazu foreach

Z důvodu zjednodušení nejsou výstupy z `foreach` akcí již zabaleny do objektu s názvem `repeatItems`. Pomocí těchto změn se navíc odeberou funkce `repeatItem()`, `repeatBody()`a `repeatOutputs()`.

Takže pomocí předchozího `repeat` příklad získáte tyto výstupy:

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

Nyní získáte tyto výstupy:

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

Dříve pro získání `body` z akce při odkazování na tyto výstupy:

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

Teď můžete místo toho použít tuto verzi:

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

Funkce naslouchacího procesu HTTP jsou teď integrované, takže nemusíte nasazovat aplikaci API pro naslouchací proces HTTP. Další informace najdete v tématu Postup při pokusu o [zpřístupnění koncového bodu aplikace logiky](../logic-apps/logic-apps-http-endpoint.md). 

Pomocí těchto změn Logic Apps nahradí funkci `@accessKeys()` funkcí `@listCallbackURL()`, která v případě potřeby získá koncový bod. V aplikaci logiky teď musíte definovat alespoň jednu Trigger. Chcete-li `/run` pracovní postup, je třeba použít jeden z těchto typů triggerů: `Manual`, `ApiConnectionWebhook`nebo `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Volání podřízených pracovních postupů

Dříve se volání podřízených pracovních postupů vyžadovalo v pracovním postupu, získání přístupového tokenu a vložení tokenu do definice aplikace logiky, kde chcete zavolat tento podřízený pracovní postup. V tomto schématu Logic Apps modul automaticky generuje SAS za běhu pro podřízený pracovní postup, takže nemusíte vkládat žádné tajné kódy do definice. Zde naleznete příklad:

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

Podřízené pracovní postupy také získají úplný přístup k příchozímu požadavku. Takže můžete předat parametry v části `queries` a v objektu `headers`. Můžete také úplně definovat celou část `body`.

V konečném případě mají podřízené pracovní postupy tyto požadované změny. I když jste mohli dříve a přímo volat podřízený pracovní postup, musíte nyní definovat koncový bod triggeru v pracovním postupu pro volání nadřazených objektů. Obecně byste přidali Trigger, který má `Manual` Type, a potom použít tuto aktivační událost v definici nadřazené položky. Vlastnost `host` specificky má `triggerName`, protože je vždy nutné zadat Trigger, který zavoláte.

## <a name="other-changes"></a>Další změny

### <a name="new-queries-property"></a>Nová vlastnost dotazy

Všechny typy akcí teď podporují nový vstup s názvem `queries`. Tento vstup může být strukturovaným objektem, nikoli sestavovat řetězec ručně.

### <a name="renamed-parse-function-to-json"></a>Přejmenovaná funkce Parse () na JSON ()

Funkce `parse()` nyní přejmenována `json()` funkce pro budoucí typy obsahu.

## <a name="enterprise-integration-apis"></a>Rozhraní API pro Podniková integrace

Toto schéma zatím nepodporuje spravované verze pro rozhraní Podniková integrace API, jako je AS2. Můžete však použít existující nasazená rozhraní API BizTalk prostřednictvím akce HTTP. Další informace najdete v tématu "použití již nasazených aplikací API" v rámci [Průvodce integrací](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
