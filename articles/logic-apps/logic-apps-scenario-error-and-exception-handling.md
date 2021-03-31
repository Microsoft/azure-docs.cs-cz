---
title: Zpracování výjimek & scénář protokolování chyb
description: Reálný případ a scénář použití pro pokročilé zpracování výjimek a protokolování chyb v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000347"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scénář: Zpracování výjimek a protokolování chyb v aplikacích logiky

Tento scénář popisuje, jak můžete rozšířit aplikaci logiky pro lepší podporu zpracování výjimek. Pro zodpovězení otázky jsme použili případ použití v reálném čase: "podporuje Azure Logic Apps výjimku a zpracování chyb?"

> [!NOTE]
> Aktuální schéma Azure Logic Apps poskytuje standardní šablonu pro reakce na akce. Tato šablona zahrnuje interní ověřování i chybové odpovědi vrácené z aplikace API.

## <a name="scenario-and-use-case-overview"></a>Scénář a případ použití – přehled

Toto je příběh jako případ použití pro tento scénář: 

Dobře známá zdravotní organizace se zapojí do vývoje řešení Azure, které by vytvořilo portál pacienty pomocí Microsoft Dynamics CRM Online. Jsou potřeba k posílání záznamů událostí mezi portálem pacientů Dynamics CRM Online a Salesforce. Požádali jsme o použití standardu [změněného HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) pro všechny záznamy pacienta.

Projekt má dva hlavní požadavky:  

* Metoda pro protokolování záznamů odeslaných z portálu Dynamics CRM Online
* Způsob zobrazení všech chyb, ke kterým došlo v rámci pracovního postupu

> [!TIP]
> Informace o tomto projektu na vysoké úrovni najdete v tématu [Skupina uživatelů integrace](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Skupina uživatelů integrace").

## <a name="how-we-solved-the-problem"></a>Jak jsme vyřešili problém

Zvolili jsme [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") jako úložiště pro záznamy protokolů a chyb (Cosmos DB odkazuje na záznamy jako dokumenty). Vzhledem k tomu, že Azure Logic Apps má standardní šablonu pro všechny odpovědi, nemusíme vytvářet vlastní schéma. Mohli jsme vytvořit aplikaci API pro **vložení** a **dotazování** na záznamy chyb a protokolů. V rámci aplikace API jsme také mohli definovat schéma pro každou z nich.  

Dalším požadavkem bylo vyprázdnění záznamů po určitém datu. Cosmos DB má vlastnost s názvem [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Doba do provozu") (TTL), která nám umožnila nastavit hodnotu **Time to Live** pro každý záznam nebo kolekci. Tato funkce eliminuje nutnost ručního odstranění záznamů v Cosmos DB.

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete vytvořit databázi Cosmos DB a dvě kolekce (protokolování a chyby).

## <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

Prvním krokem je vytvoření aplikace logiky a otevření aplikace v návrháři aplikace logiky. V tomto příkladu používáme aplikace logiky nadřazený-podřízený. Řekněme, že už jsme vytvořili nadřazený objekt a chystáme se vytvořit jednu podřízenou aplikaci logiky.

Vzhledem k tomu, že budeme protokolovat záznam z Dynamics CRM Online, pojďme začít v horní části. Je nutné použít Trigger **žádosti** , protože nadřazená aplikace logiky tuto podřízenou proceduru spustí.

### <a name="logic-app-trigger"></a>Trigger aplikace logiky

Používáme aktivační událost **požadavku** , jak je znázorněno v následujícím příkladu:

``` json
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

```


## <a name="steps"></a>Postup

Je potřeba protokolovat zdroj (žádost) záznamu pacienta z portálu Dynamics CRM Online.

1. Je potřeba získat nový záznam o schůzce z Dynamics CRM Online.

   Aktivační událost přicházející z CRM poskytuje US **PatentId**, **typ záznamu**, **Nový nebo aktualizovaný záznam** (novou nebo aktualizovat logickou hodnotu) a **SalesforceId**. **SalesforceId** může mít hodnotu null, protože se používá pouze pro aktualizaci.
   Záznam CRM získáme pomocí **PATIENTID** CRM a **typu záznamu**.

2. Dál je potřeba přidat naši Azure Cosmos DB operaci InsertLogEntry API App  , jak je znázorněno v návrháři aplikace logiky.

   **Vložit položku protokolu**

   ![Snímek obrazovky z návrháře aplikace logiky ukazující nastavení konfigurace pro InsertLogEntry.](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Vložit položku chyby**

   ![Snímek obrazovky z návrháře aplikace logiky ukazující nastavení konfigurace pro CreateErrorRecord.](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Vyhledat selhání při vytváření záznamu**

   ![Snímek obrazovky CreateErrorRecord v návrháři aplikace logiky zobrazující pole pro vytvoření položky chyby](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Zdrojový kód aplikace logiky

> [!NOTE]
> Následující příklady jsou pouze ukázky. Vzhledem k tomu, že je tento kurz založený na implementaci nyní v produkčním prostředí, hodnota **zdrojového uzlu** nemusí zobrazovat vlastnosti související s plánováním schůzky. > 

### <a name="logging"></a>protokolování

Následující ukázka kódu aplikace logiky ukazuje, jak zpracovat protokolování.

#### <a name="log-entry"></a>Položka protokolu

Zde je zdrojový kód aplikace logiky pro vložení položky protokolu.

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

#### <a name="log-request"></a>Protokolovat požadavek

Tady je zpráva s požadavkem protokolu, která se publikuje do aplikace API.

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


#### <a name="log-response"></a>Reakce protokolu

Tady je zpráva s odpovědí protokolu z aplikace API.

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

Teď se podíváme na postup zpracování chyb.

### <a name="error-handling"></a>Zpracování chyb

Následující ukázka kódu aplikace logiky ukazuje, jak můžete implementovat zpracování chyb.

#### <a name="create-error-record"></a>Vytvořit záznam chyby

Zde je zdrojový kód aplikace logiky pro vytvoření záznamu chyby.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Vložení chyby do Cosmos DB--Request

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

#### <a name="insert-error-into-cosmos-db--response"></a>Vložení chyby do Cosmos DB--Response

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

#### <a name="salesforce-error-response"></a>Chybná odpověď Salesforce

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Vrátit odpověď zpět do nadřazené aplikace logiky

Po získání odpovědi můžete odpověď předat zpátky do nadřazené aplikace logiky.

#### <a name="return-success-response-to-parent-logic-app"></a>Vrátit zpět odpověď na nadřazenou aplikaci logiky

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

#### <a name="return-error-response-to-parent-logic-app"></a>Vrácení chybové odpovědi do nadřazené aplikace logiky

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


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB úložiště a portál

Naše řešení přidalo možnosti s [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portál pro správu chyb

Chcete-li zobrazit chyby, můžete vytvořit webovou aplikaci MVC k zobrazení záznamů o chybách z Cosmos DB. Aktuální verze obsahuje operace **seznamu**, **podrobností**, **Úpravy** a **odstranění** .

> [!NOTE]
> Upravit operaci: Cosmos DB nahradí celý dokument. Záznamy zobrazené v zobrazení **seznam** a **Podrobnosti** jsou pouze ukázky. Nejedná se o skutečné záznamy událostí pacienta.

Tady jsou příklady našich podrobností aplikace MVC vytvořených pomocí dříve popsaného přístupu.

#### <a name="error-management-list"></a>Seznam správy chyb
![Seznam chyb](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Podrobné zobrazení správy chyb
![Podrobnosti o chybě](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portál pro správu protokolů

Pro zobrazení protokolů jsme také vytvořili webovou aplikaci MVC. Tady jsou příklady našich podrobností aplikace MVC vytvořených pomocí dříve popsaného přístupu.

#### <a name="sample-log-detail-view"></a>Ukázkové zobrazení podrobného protokolu
![Zobrazení podrobností protokolu](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Podrobnosti aplikace API

#### <a name="logic-apps-exception-management-api"></a>Rozhraní API pro správu výjimek Logic Apps

Naše open source aplikace API Management Azure Logic Apps poskytuje funkce popsané tady – existují dva řadiče:

* **ErrorController** vloží záznam o chybě (dokument) do kolekce Azure Cosmos DB.
* **LogController** Vloží záznam protokolu (dokument) do kolekce Azure Cosmos DB.

> [!TIP]
> Oba řadiče využívají `async Task<dynamic>` operace, což umožňuje operacím vyřešit za běhu, takže můžeme v těle operace vytvořit Azure Cosmos DB schéma. 
> 

Každý dokument v Azure Cosmos DB musí mít jedinečné ID. Používáme `PatientId` a přidáváme časové razítko, které se převede na hodnotu časového razítka systému UNIX (Double). Zkrátí hodnotu, aby se odstranila desetinná hodnota.

Zdrojový kód našeho rozhraní API pro chybový adaptér můžete zobrazit z [GitHubu](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Rozhraní API zavoláme z aplikace logiky pomocí následující syntaxe:

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

Výraz v předchozí ukázce kódu kontroluje *Create_NewPatientRecord* stav **selhání**.

## <a name="summary"></a>Souhrn

* V aplikaci logiky můžete snadno implementovat protokolování a zpracování chyb.
* Jako úložiště pro záznamy protokolů a chyb (dokumenty) můžete použít Azure Cosmos DB.
* Pomocí MVC můžete vytvořit portál pro zobrazení záznamů protokolů a chyb.

### <a name="source-code"></a>Zdrojový kód

Zdrojový kód pro aplikaci rozhraní API pro správu výjimek Logic Apps je k dispozici v tomto [úložišti GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Rozhraní API pro správu výjimek aplikace logiky").

## <a name="next-steps"></a>Další kroky

* [Zobrazit další příklady a scénáře aplikace logiky](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps.md)
* [Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
