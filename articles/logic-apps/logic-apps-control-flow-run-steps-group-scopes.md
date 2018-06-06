---
title: Přidat obory, které spouštění akcí na základě stavu skupiny - Azure Logic Apps | Microsoft Docs
description: Postup vytvoření obory, které spustit pracovní postup akce na základě stavu akce skupiny v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: c6f6b54e17d12ff21d50748810699e78e3a14757
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726376"
---
# <a name="create-scopes-that-run-workflow-actions-based-on-group-status-in-azure-logic-apps"></a>Vytvoření obory, které spustit pracovní postup akce na základě stavu skupiny v Azure Logic Apps

Ke spouštění akcí, až po jiná skupina akce úspěch nebo neúspěch, skupina tyto akce uvnitř *oboru*. Tato struktura je užitečné, když chcete uspořádat akce logické skupiny, vyhodnocení stavu pro tuto skupinu a provádět akce, které jsou založené na stav oboru. Po dokončení spuštění všechny akce v oboru oboru také získá svůj vlastní stav. Například můžete pomocí oborů, pokud chcete implementovat [výjimky a zpracování chyb](../logic-apps/logic-apps-exception-handling.md#scopes). 

Chcete-li zkontrolovat stav oboru, můžete použít stejné kritéria, která slouží k určení logiku aplikace spustit stavu, jako je například "Succeeded", "Se nezdařilo", "Zrušeno" a tak dále. Ve výchozím nastavení když je akce všechny oboru úspěšné, stav oboru označena "Succeeded". Ale když selže veškeré akce v oboru, nebo je zrušena, je oboru stav označen "Se nezdařilo." Omezení u oborů, najdete v části [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Zde je třeba aplikaci vysoké úrovně logiku, která používá obor ke spuštění určitých akcí a podmínku pro kontrolu stavu oboru. Pokud všechny akce v oboru selhat nebo ukončení neočekávaně, oboru je označen "Failed" nebo "Přerušené" v uvedeném pořadí a logiku aplikace odešle zprávu "Oboru se nezdařilo". Pokud všechny vymezená akce úspěšné, aplikaci logiky odešle zprávu "Oboru bylo úspěšně dokončeno".

![Nastavit aktivační události "Plán opakování –"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Požadavky

V našem příkladu v tomto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* E-mailový účet ze zprostředkovatele všechny e-mailu podporuje Logic Apps. Tento příklad používá Outlook.com. Pokud chcete použít k jinému zprostředkovateli, obecný tok zůstane stejný, ale uživatelské rozhraní se zobrazí různé.

* Klíč mapy Bing. Chcete-li získat tento klíč, přečtěte si téma <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">získání klíče mapy Bing</a>.

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Vytvoření ukázkové aplikace logiky

Nejprve vytvořte této ukázkové aplikace logiky, abyste později přidat obor:

![Vytvoření ukázkové aplikace logiky](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **plán - opakování** aktivační událost, která kontroluje službu Bing Maps na časový interval, který zadáte
* A **mapy Bing - Get trasy** akce, která kontroluje cesta čas mezi dvěma umístěními
* Podmíněné příkaz, který kontroluje, zda cesta čas překročí zadaný doba
* Akce, která odešle že e-mailu tohoto aktuální čas cesta překročí zadané časové

Můžete kdykoli uložit svou aplikaci logiky, často, uložte si práci.

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">portál Azure</a>, pokud jste tak ještě neučinili. Vytvoření prázdné aplikace logiky

2. Přidat **plán - opakování** aktivační událost s těmito nastaveními: **Interval** = "1" a **frekvence** = "Minutu"

   ![Nastavit aktivační události "Plán opakování –"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Vizuální zjednodušují zobrazení a skrytí podrobnosti každá akce v návrháři, sbalte obrazce každá akce jako průběh pomocí těchto kroků.

3. Přidat **mapy Bing - Get trasy** akce. 

   1. Pokud ještě nemáte připojení mapy Bing, budete vyzváni k vytvoření připojení.

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Název připojení** | PřipojeníMapyBing | Zadejte název připojení. | 
      | **Klíč rozhraní API** | <*klíč-služby-Mapy-Bing*> | Zadejte klíč Map Bing, který jste dříve dostali. | 
      ||||  

   2. Nastavení vaší **Get trasy** akce, jak je znázorněno v tabulce níže tuto bitovou kopii:

      ![Nastavení "Mapy Bing - Get trasy" akce](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Další informace o těchto parametrech najdete v tématu [Výpočet trasy](https://msdn.microsoft.com/library/ff701717.aspx).

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Bod na trase 1** | <*Spuštění*> | Zadejte zdroj vaší trasy. | 
      | **Bod na trase 2** | <*End*> | Zadejte cílový vaší trasy. | 
      | **Vyloučit** | Žádný | Zadejte položky, pokud chcete zabránit v postupu, jako je například dálnice, mýtné a tak dále. Možné hodnoty, najdete v části [vypočítat trasu](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimalizovat** | timeWithTraffic | Vyberte parametr, který se optimalizovat směrování, jako jsou například vzdálenost, čas aktuální informace o provozu a tak dále. Tento příklad používá tuto hodnotu: "timeWithTraffic" | 
      | **Jednotka vzdálenosti** | <*vaše-volba*> | Zadejte jednotku vzdálenosti k výpočtu trasu. Tento příklad používá tuto hodnotu: "Míle" | 
      | **Způsob cestování** | Autem | Zadejte režim cesta pro trasu. Tento příklad používá tato hodnota "Řidičských" | 
      | **Datum a čas přejezdu** | Žádný | Platí pro pouze v režimu přenosu. | 
      | **Typ přenosu typu datum** | Žádný | Platí pro pouze v režimu přenosu. | 
      ||||  

4. Přidejte podmínku do aškrtněte, zda aktuální čas cesta s přenosy překročí určitou dobu. V tomto příkladu postupujte podle kroků v části tuto bitovou kopii:

   ![Vytvořit podmínky](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Přejmenujte podmínky se tento popis: **Pokud provoz čas více než zadanou dobu**

   2. Seznam parametrů, vyberte **cesta doba provozu** pole, která je v sekundách. 

   3. Relační operátor, vyberte tento operátor: **je větší než**

   4. Zadejte pro hodnotu porovnání **600**, což je v sekundách a ekvivalentem hodnoty na 10 minut.

5. V podmínce pro **v případě hodnoty true** větev, přidat akci "odeslání e-mailu" pro váš poskytovatel e-mailu. Nastavte tuto akci s podrobnostmi, jak je znázorněno v tabulce v části tuto bitovou kopii:

   ![Přidání "Odeslat e-mailu" akce "je-li nastavena hodnota true" firemní pobočky](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Pro **k** zadejte e-mailovou adresu pro účely testování.

   2. Pro **subjektu** pole, zadejte tento text:

      ```Time to leave: Traffic more than 10 minutes```

   3. Pro **textu** pole, zadejte tento text se počátečních a koncových znaků: 

      ```Travel time: ```

      Při kurzor se zobrazí v **textu** pole, seznamu dynamického obsahu zůstane otevřený, ve kterém můžete vybrat žádné parametry, které jsou k dispozici v tomto okamžiku.

   4. V seznamu dynamického obsahu vyberte **Výraz**.

   5. Najděte a vyberte **(div)** funkce.

   6. Kurzor je v závorkách funkce, vyberte **dynamický obsah** tak, aby můžete přidat **provozu doba provozu** parametr další.

   7. V části **Get trasy** vyberte v seznamu dynamický parametr **provozu doba provozu** pole.

      ![Vyberte "Provozu doba provozu"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Po pole přeloží do formátu JSON, přidat **čárkami** (```,```) a číslo ```60``` tak, aby převodu hodnoty v **provozu doba provozu** z sekund až minut. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Výraz teď vypadá v tomto příkladu:

      ![Dokončit výraz](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Ujistěte se, že zvolíte **OK** po dokončení.

  10. Po výraz řeší, přidejte tento text se úvodní mezery: ``` minutes```
  
      Vaše **textu** pole bude vypadat jako tento ukázkový:

      ![Dokončení pole "Body"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Uložte svou aplikaci logiky.

Dál přidejte obor, aby mohli skupiny konkrétní akce a vyhodnotit jejich stav.

## <a name="add-a-scope"></a>Přidat obor

1. Pokud jste to ještě neudělali, otevřete aplikaci logiky v návrháři aplikace logiky. 

2. V umístění pracovního postupu, který chcete přidáte obor. Příklad:

   * Chcete-li přidat obor mezi existující kroky v pracovním postupu aplikace logiky, přesuňte ukazatel přes na šipku, ve které chcete přidat obor. 
   Vyberte **znaménko plus** (**+**) > **přidat obor**.

     ![Přidat obor](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Pokud chcete přidat nový obor na konci pracovního postupu, v dolní části svou aplikaci logiky, vyberte **+ nový krok** > **... Další** > **přidat obor**.

3. Nyní přidat kroky nebo přetáhněte stávající kroky, které chcete spustit uvnitř oboru. V tomto příkladu přetáhněte do oboru tyto akce:
      
   * **Získat trasy**
   * **Pokud provoz čas více než určitou dobu**, což zahrnuje i **true** a **false** větví

   Aplikace logiky teď vypadá v tomto příkladu:

   ![Přidat obor](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. V oboru přidejte podmínku, která ověří stav oboru. Přejmenujte podmínky se tento popis: **Pokud oboru se nezdařilo**

   ![Přidat podmínku pro kontrolu stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. Sestavení tento výraz, který zkontroluje, zda je oboru stav rovna `Failed` nebo `Aborted`.

   ![Přidat výraz, který ověří stav oboru](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   Nebo, pokud chcete zadat Tento výraz jako text, zvolte **upravit v rozšířeném režimu**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. V **v případě hodnoty true** a **-li pravda** větví, přidejte akce, které chcete provést, například odeslání e-mailu nebo zprávy.

   ![Přidat výraz, který ověří stav oboru](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Uložte svou aplikaci logiky.

Aplikace logiky dokončení teď vypadá v tomto příkladu všechny obrazce rozšířit:

![Aplikace logiky bylo dokončeno s oborem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testování práci

Na panelu nástrojů návrháře zvolte **spustit**. Pokud všechny vymezená akce úspěšné, se zobrazí zpráva "Oboru bylo úspěšně dokončeno". Pokud všechny akce, oboru není úspěšné, se zobrazí zpráva "Oboru se nezdařilo". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definici JSON

Pokud pracujete v zobrazení kódu, můžete definovat strukturu oboru v definici aplikace logiky JSON místo. Zde je ukázka, definici JSON pro aktivační události a akce v předchozí aplikaci logiky:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Odeslání nebo hlasovat o funkcích a návrhy, najdete [web pro zasílání názorů uživatele Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazech switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (smyčky)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
