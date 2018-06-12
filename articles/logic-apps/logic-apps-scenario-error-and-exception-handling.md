---
title: Zpracovávání výjimek v jazyce & scénář protokolování chyb – Azure Logic Apps | Microsoft Docs
description: Popisuje případu použití skutečné o pokročilé zpracování výjimek a protokolování chyb pro Azure Logic Apps
keywords: ''
services: logic-apps
author: hedidin
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/29/2016
ms.author: LADocs; b-hoedid
ms.openlocfilehash: cb80423266d3e9c0b3cac31821965ad92c0420d9
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299354"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scénář: Zpracování výjimek a protokolování chyb pro logic apps

Tento scénář popisuje, jak můžete rozšířit aplikace logiky lepší podpory výjimek. Jste použili jsme případu použití reálnými odpověď na otázku: "Azure Logic Apps podporuje výjimky a zpracování chyb?"

> [!NOTE]
> Aktuální schéma Azure Logic Apps poskytuje standardní šablona pro akce odpovědi. Tato šablona obsahuje interní ověření a chybové odpovědi vrácená z aplikace API.

## <a name="scenario-and-use-case-overview"></a>Přehled scénáře a použití případu

Tady je článek jako případ použití pro tento scénář: 

Dobře známé zdravotní péče organizace pověření nám vyvíjet Azure řešení, které by vytvořit pacienta portálu pomocí Microsoft Dynamics CRM Online. Potřebovali odeslat schůzku záznamy mezi Dynamics CRM Online pacienta portál a Salesforce. Jsme se zobrazí dotaz, použít [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standard pro všechny pacienta záznamy.

Projekt má dva hlavní požadavky:  

* Metoda na protokolování záznamů odeslaných z portálu pro Dynamics CRM Online
* Způsob, jak zobrazit chyby, ke kterým došlo v rámci pracovního postupu

> [!TIP]
> Souhrnné video o tomto projektu najdete v tématu [skupiny uživatelů integrace](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "skupiny uživatelů integrace").

## <a name="how-we-solved-the-problem"></a>Jak jsme problém byl

Jsme zvolili [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") jako úložiště pro záznamy protokolu a chyby (Cosmos DB odkazuje na záznamy jako dokumenty). Azure Logic Apps obsahuje standardní šablonu pro všechny odpovědi, a proto jsme nebude muset vytvořit vlastní schéma. Vytvoříme může aplikace API k **vložit** a **dotazu** pro záznamy chyba a protokolu. Může také definujeme schéma pro jednotlivé aplikace API.  

Další požadavky se k vyprázdnění záznamů po určitém datu. Cosmos DB má vlastnost s názvem [TTL](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "TTL") (TTL), což nám nastavit povolené **TTL** hodnotu pro každý záznam nebo kolekce. Tato funkce eliminovat potřeba ručně odstranit záznamy v databázi Cosmos.

> [!IMPORTANT]
> K dokončení tohoto kurzu, musíte vytvořit databázi Cosmos DB a dvě kolekce (protokolování a chyby).

## <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

Prvním krokem je vytvoření aplikace logiky a otevřete aplikaci v návrháři aplikace logiky. V tomto příkladu používáme aplikace logiky nadřazený podřízený. Předpokládejme, že jsme už máte vytvořené nadřazené a se chystáte vytvořit jednu aplikaci logiky podřízené.

Vzhledem k tomu, že přidáme protokolu záznam vycházejících z Dynamics CRM Online, Začněme v horní části. Musí používáme **požadavku** aktivovat, protože aplikace logiky nadřazené aktivuje tohoto dítěte.

### <a name="logic-app-trigger"></a>Trigger aplikace logiky

Používáme **požadavku** spustit jak je znázorněno v následujícím příkladu:

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


## <a name="steps"></a>Kroky

Zdroj (požadavek) pacienta záznamu jsme musíte se přihlásit z portálu Dynamics CRM Online.

1. Nový záznam schůzku nám musí získat ze služby Dynamics CRM Online.

   Aktivační události pocházející z CRM poskytuje nám s **CRM PatentId**, **typ záznamu**, **nový nebo aktualizovat záznam** (nový nebo aktualizovat logická hodnota), a **SalesforceId**. **SalesforceId** může mít hodnotu null, protože se používá pouze pro aktualizaci.
   Se nám získat záznam CRM pomocí aplikace CRM **PatientID** a **typ záznamu**.

2. Dále je potřeba přidat vaší aplikace Azure Cosmos DB SQL API **InsertLogEntry** operace, jak je vidět tady v návrháři aplikace logiky.

   **Vložit položky protokolu**

   ![Vložit položky protokolu](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Vložit položku chyby**

   ![Vložit položky protokolu](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Kontrola pro vytvoření záznamu selhání**

   ![Podmínka](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Zdrojovém kódu aplikace logiky

> [!NOTE]
> Následující příklady jsou pouze ukázky. Protože v tomto kurzu vychází z implementace teď v produkčním prostředí, hodnotu **zdrojový uzel** se nemusí zobrazit vlastnosti, které se vztahují k plánování na schůzku. > 

### <a name="logging"></a>Protokolování

Následující ukázka kódu aplikace logiky ukazuje způsob zpracování protokolování.

#### <a name="log-entry"></a>Položka protokolu

Tady je zdrojovém kódu aplikace logiky pro vkládání položka protokolu.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Žádost protokolu

Zde je zpráva protokolu žádost odeslat do aplikace API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Odpověď protokolu

Zde je zpráva odpovědi protokolu z aplikace API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Nyní Podíváme se na zpracování kroky chyb.

### <a name="error-handling"></a>Zpracování chyb

Následující ukázka kódu aplikace logiky ukazuje, jak můžete implementovat zpracování chyb.

#### <a name="create-error-record"></a>Vytvořit záznam chyby

Tady je zdrojovém kódu aplikace logiky pro vytvoření záznam chyby.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Chyba vložit do DB Cosmos – požadavku

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Chyba vložit do databáze Cosmos – odpověď

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce chybové odpovědi

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Vrátí odpověď zpět do nadřazené aplikace logiky

Po získání odpovědi můžete předat odpověď zpět do aplikace logiky nadřazené.

#### <a name="return-success-response-to-parent-logic-app"></a>Vrátí úspěšná odpověď do nadřazené logiku aplikace

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Vrátí chybové odpovědi do nadřazené logiku aplikace

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Úložiště cosmos DB a portálu

Naše řešení možnosti s přidané [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portál pro správu chyby

Chcete-li zobrazit chyby, můžete vytvořit webové aplikace MVC zobrazí chyba záznamy z databáze Cosmos. **Seznamu**, **podrobnosti**, **upravit**, a **odstranit** operace jsou zahrnuté v aktuální verzi.

> [!NOTE]
> Upravit operace: Cosmos DB nahradí celý dokument. Záznamy zobrazené na **seznamu** a **podrobností** zobrazení jsou pouze vzorky. Nejsou skutečné pacienta schůzku záznamy.

Zde jsou příklady naše podrobnosti o aplikaci MVC vytvořené pomocí výše popsaných přístup.

#### <a name="error-management-list"></a>Seznam chyb správy
![Seznam chyb](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Zobrazení podrobností chyb správy
![Podrobnosti o chybě](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portál pro správu protokolu

K zobrazení protokolů, také jsme vytvořili webové aplikace MVC. Zde jsou příklady naše podrobnosti o aplikaci MVC vytvořené pomocí výše popsaných přístup.

#### <a name="sample-log-detail-view"></a>Zobrazení podrobností protokolu ukázka
![Zobrazení podrobností protokolu](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Podrobnosti o aplikaci API

#### <a name="logic-apps-exception-management-api"></a>Rozhraní API správy výjimky aplikace logiky

Naše open-source Azure Logic Apps Výjimka rozhraní API pro správu aplikací poskytuje funkci podle postupu popsaného tady – existují dva řadiče:

* **ErrorController** vloží záznam chyby (dokument) v kolekci Azure Cosmos DB.
* **LogController** vloží záznam protokolu (dokument) v kolekci Azure Cosmos DB.

> [!TIP]
> Použít oba řadiče `async Task<dynamic>` operace a operace přeložit za běhu, takže jsme můžete vytvořit schéma databáze Cosmos Azure v těle operace. 
> 

Každému dokumentu v Azure Cosmos DB musí mít jedinečné ID. Používáme `PatientId` a přidání časové razítko, které jsou převedeny na hodnotu časového razítka systému Unix (double). Jsme zkrátit hodnota odebrat desetinnou hodnotu.

Můžete zobrazit zdrojový kód chyby kontroleru rozhraní API [z Githubu](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Jsme volání rozhraní API z aplikace logiky pomocí následující syntaxe:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Vyhledá výraz v předchozím příkladu kódu *Create_NewPatientRecord* stav **se nezdařilo**.

## <a name="summary"></a>Souhrn

* Můžete snadno implementovat protokolování a zpracování chyb v aplikaci logiky.
* Můžete použít Azure Cosmos DB jako úložiště pro záznamy protokolu a chyby (dokumentů).
* MVC slouží k vytvoření portálu zobrazit záznamy protokolu a chyby.

### <a name="source-code"></a>Zdrojový kód

Zdrojový kód pro správu výjimek aplikace logiky aplikace rozhraní API je k dispozici v tomto [úložiště GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "rozhraní API pro správu aplikace logiky aplikace výjimka").

## <a name="next-steps"></a>Další postup

* [Zobrazit další logiku aplikace příkladů a scénářů](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Další informace o sledování aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Vytvoření šablony pro automatické nasazení pro logic apps](../logic-apps/logic-apps-create-deploy-template.md)
