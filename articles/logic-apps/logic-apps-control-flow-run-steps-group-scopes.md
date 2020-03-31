---
title: Seskupení a spuštění akcí podle oboru
description: Vytvoření akcí s vymezenou oborem, které běží na základě stavu skupiny v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74791489"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Spouštění akcí na základě stavu skupiny pomocí oborů v Azure Logic Apps

Chcete-li spustit akce pouze po úspěšné nebo neúspěšné jiné skupině akcí, seskupte tyto akce do *oboru*. Tato struktura je užitečná, pokud chcete uspořádat akce jako logickou skupinu, vyhodnotit stav této skupiny a provádět akce, které jsou založeny na stavu oboru. Po dokončení všech akcí v oboru dokončení oboru obor také získá svůj vlastní stav. Obory můžete například použít, pokud chcete implementovat [výjimku a zpracování chyb](../logic-apps/logic-apps-exception-handling.md#scopes). 

Chcete-li zkontrolovat stav oboru, můžete použít stejná kritéria, která používáte k určení stavu spuštění aplikace logiky, například "Proběhlo", "Nezdařilo se", "Zrušeno" a tak dále. Ve výchozím nastavení, pokud jsou všechny akce oboru úspěšné, je stav oboru označen jako "Úspěšné". Ale při jakékoli akce v oboru selže nebo je zrušena, stav oboru je označen "Nezdařilo se". Omezení oborů naleznete v tématu [Omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Například zde je aplikace logiky vysoké úrovně, která používá obor ke spuštění určitých akcí a podmínku pro kontrolu stavu oboru. Pokud všechny akce v oboru nezdaří nebo konec neočekávaně, obor je označen "Nepodařilo" nebo "Přerušeno" v uvedeném pořadí a aplikace logiky odešle zprávu "Obor se nezdařilo". Pokud jsou všechny akce s vymezeným oborem úspěšné, aplikace logiky odešle zprávu "Obor proběhl úspěšně".

![Nastavit aktivační událost "Plán - opakování"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle příkladu v tomto článku, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* E-mailový účet od libovolného poskytovatele e-mailu podporovaného logicapps. Tento příklad používá Outlook.com. Pokud používáte jiného zprostředkovatele, obecný tok zůstane stejný, ale vaše ui se zobrazí jinak.

* Klíč Mapy Bing. Chcete-li získat tento klíč, přečtěte si téma <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Získání klávesy Mapy Bing</a>.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Vytvoření ukázkové aplikace logiky

Nejprve vytvořte tuto ukázkovou aplikaci logiky, abyste později mohli přidat obor:

![Vytvoření ukázkové aplikace logiky](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Aktivační **událost Plán – opakování,** která kontroluje službu Mapy Bing v intervalu, který zadáte.
* **Mapy Bing – akce získání trasy,** která kontroluje dobu jízdy mezi dvěma místy
* Podmíněný příkaz, který kontroluje, zda doba jízdy překračuje zadanou dobu jízdy
* Akce, která vám pošle e-mail, že aktuální doba jízdy překročí zadaný čas

Aplikaci logiky můžete kdykoli uložit, takže svou práci ukládejte často.

1. Pokud jste to ještě neudělali, přihlaste se na <a href="https://portal.azure.com" target="_blank">portál Azure.</a> Vytvoření prázdné aplikace logiky

1. Přidat **aktivační událost Plán - Opakování** s těmito nastaveními: **Interval** = "1" a **Frekvence** = "Minuta"

   ![Nastavit aktivační událost "Plán - opakování"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Chcete-li vizuálně zjednodušit zobrazení a skrýt podrobnosti jednotlivých akcí v návrháři, sbalte obrazec každé akce při procházení těmito kroky.

1. Přidejte mapy Bing - získat akci **trasy.** 

   1. Pokud ještě nemáte připojení Mapy Bing, budete vyzváni k vytvoření připojení.

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Název připojení** | PřipojeníMapyBing | Zadejte název připojení. | 
      | **Klíč rozhraní API** | <*vaše-Bing-Mapy-klíč*> | Zadejte klíč Map Bing, který jste dříve dostali. | 
      ||||  

   1. Nastavte akci **Získat trasu,** jak je znázorněno v tabulce pod tímto obrázkem:

      ![Nastavit akci Mapy Bingu – získat trasu](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Další informace o těchto parametrech najdete v tématu [Výpočet trasy](https://msdn.microsoft.com/library/ff701717.aspx).

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Bod na trase 1** | <*Spustit*> | Zadejte původ trasy. | 
      | **Bod na trase 2** | <*Konec*> | Zadejte cíl trasy. | 
      | **Vyloučit** | Žádný | Zadejte položky, kterým se chcete na trase vyhnout, například dálnice, mýta a tak dále. Možné hodnoty naleznete [v tématu Výpočet trasy](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimalizovat** | timeWithTraffic | Vyberte parametr pro optimalizaci trasy, například vzdálenost, čas s aktuálními dopravními informacemi a tak dále. Tento příklad používá tuto hodnotu: "timeWithTraffic" | 
      | **Jednotka vzdálenosti** | <*vaše preference*> | Zadejte jednotku vzdálenosti pro výpočet trasy. Tento příklad používá tuto hodnotu: "Mile" | 
      | **Způsob cestování** | Autem | Zadejte způsob jízdy pro vaši trasu. Tento příklad používá tuto hodnotu "Řízení" | 
      | **Datum a čas přejezdu** | Žádný | Platí pouze pro režim přenosu. | 
      | **Typ typu Datum tranzitu** | Žádný | Platí pouze pro režim přenosu. | 
      ||||  

1. [Přidejte podmínku,](../logic-apps/logic-apps-control-flow-conditional-statement.md) která kontroluje, zda aktuální doba jízdy s provozem překračuje zadaný čas. 
   V tomto příkladu postupujte takto:

   1. Přejmenování podmínky s tímto popisem: **Pokud je doba provozu delší než zadaný čas**

   1. V levém sloupci klikněte do pole **Zvolit hodnotu,** aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte pole **Cestovní doba provozu,** které je v sekundách. 

      ![Vytvoření podmínky](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Ve středním poli vyberte tento operátor: **je větší než**

   1. Do sloupce zcela vpravo zadejte tuto srovnávací hodnotu, která je v sekundách a odpovídá 10 minutám: **600**

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Dokončená podmínka](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Ve větvi **Pokud je to pravda,** přidejte akci "odeslat e-mail" pro svého poskytovatele e-mailu. 
   Tuto akci nastavte podle kroků na tomto obrázku:

   ![Přidání akce Odeslat e-mail do větve "Pokud je to pravda"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Do pole **Do** zadejte svou e-mailovou adresu pro účely testování.

   1. Do pole **Předmět** zadejte tento text:

      ```Time to leave: Traffic more than 10 minutes```

   1. Do pole **Tělo** zadejte tento text s koncovým prostorem: 

      ```Travel time:```

      Zatímco se kurzor zobrazí v poli **Tělo,** zůstane seznam dynamického obsahu otevřený, takže můžete vybrat všechny parametry, které jsou v tomto okamžiku k dispozici.

   1. V seznamu dynamického obsahu vyberte **Výraz**.

   1. Najít a vybrat **div()** funkce. 
      Vložte kurzor do závorek funkce.

   1. Když je kurzor uvnitř závorek funkce, zvolte **Dynamický obsah,** aby se zobrazil seznam dynamického obsahu. 
   
   1. V části **Získat trasu** vyberte pole **Dopravní doba provozu.**

      ![Vyberte možnost "Provoz na dobu provozu"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Poté, co se pole překládá do formátu JSON, přidejte **čárku** (```,```) následovanou číslem, ```60``` abyste převedli hodnotu v provozu doba trvání **provozu** z sekund na minuty. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Výraz nyní vypadá takto:

      ![Dokončit výraz](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Po dokončení vyberte **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Po vyřešení výrazu přidejte tento text s mezerou pro kladné proklad:``` minutes```
  
       Pole **Tělo** nyní vypadá takto:

       ![Dokončené pole "Tělo"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Uložte svou aplikaci logiky.

Dále přidejte obor, abyste mohli seskupit konkrétní akce a vyhodnotit jejich stav.

## <a name="add-a-scope"></a>Přidání oboru

1. Pokud jste tak ještě neučinili, otevřete aplikaci logiky v Návrháři aplikací logiky. 

1. Přidejte obor do požadovaného umístění pracovního postupu. Chcete-li například přidat obor mezi existující kroky v pracovním postupu aplikace logiky, postupujte takto: 

   1. Přesuňte ukazatel myši na šipku, kam chcete přidat obor. 
   Zvolte **znaménko plus** (**+**) > Přidat **akci**.

      ![Přidání oboru](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Do vyhledávacího pole zadejte jako filtr "obor". 
   Vyberte akci **Obor.**

## <a name="add-steps-to-scope"></a>Přidání kroků do oboru

1. Nyní přidejte kroky nebo přetáhněte existující kroky, které chcete spustit uvnitř oboru. V tomto příkladu přetáhněte tyto akce do oboru:
      
   * **Získat trasu**
   * **Pokud je doba provozu delší než zadaný čas**, který zahrnuje **true** i **false** větve

   Vaše aplikace logiky teď vypadá jako tento příklad:

   ![Byl přidán obor.](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Pod obor přidejte podmínku, která kontroluje stav oboru. Přejmenovat podmínku s tímto popisem: **Pokud se obor nezdařil**

   ![Přidání podmínky pro kontrolu stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Do podmínky přidejte tyto výrazy, které zkontrolují, zda je stav oboru roven "Nezdařilo se" nebo "Přerušeno". 

   1. Chcete-li přidat další řádek, zvolte **Přidat**. 

   1. V každém řádku klikněte do levého pole, aby se zobrazil seznam dynamického obsahu. 
   Ze seznamu dynamického obsahu zvolte **Expression**. Do textového pole zadejte tento výraz a pak zvolte **OK**: 
   
      `result('Scope')[0]['status']`

      ![Přidat výraz, který kontroluje stav oboru](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Pro oba řádky je select **rovna** jako operátor. 
   
   1. Pro porovnávací hodnoty zadejte `Failed`do prvního řádku . 
   Do druhého řádku `Aborted`zadejte . 

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Přidat výraz, který kontroluje stav oboru](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Nyní nastavte `runAfter` vlastnost podmínky tak, aby podmínka zkontroluje stav oboru a spustí odpovídající akci, kterou definujete v pozdějších krocích.

   1. V **podmínce Pokud se obor nezdařil,** zvolte tři **tečky** (...) tlačítko a pak zvolte **Konfigurovat spustit po**.

      ![Konfigurace vlastnosti runAfter](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Vyberte všechny tyto stavy oboru: **je úspěšný**, **se nezdařilo**, **je přeskočeno**a **časový rozsah**

      ![Vybrat stavy oboru](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Jakmile budete hotovi, zvolte **Done** (Hotovo). 
   Podmínka nyní zobrazuje ikonu "informace".

1. V větvích **If true** a **If false** přidejte akce, které chcete provést na základě jednotlivých stavů oboru, například odešlete e-mail nebo zprávu.

   ![Přidání akcí, které je třeba provést na základě stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Uložte svou aplikaci logiky.

Dokončená aplikace logiky teď vypadá takto:

![Dokončená aplikace logiky s rozsahem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Otestujte svou práci

Na panelu nástrojů návrháře zvolte **Spustit**. Pokud všechny akce s vymezeným oborem úspěšné, zobrazí se zpráva "Obor proběhl úspěšně". Pokud žádné akce s rozsahem neuspějí, zobrazí se zpráva "Obor se nezdařil". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definice JSON

Pokud pracujete v zobrazení kódu, můžete místo toho definovat strukturu oboru v definici JSON aplikace logiky. Například zde je definice JSON pro aktivační událost a akce v předchozí aplikaci logiky:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
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
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
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
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
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
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
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
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
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
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete odeslat nebo hlasovat o funkcích a návrzích, navštivte [web pro zpětnou vazbu uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (smyček)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelních kroků (větví)](../logic-apps/logic-apps-control-flow-branches.md)
