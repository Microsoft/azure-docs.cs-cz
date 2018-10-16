---
title: Přidejte obory, na kterých běží akce na základě stavu skupiny – Azure Logic Apps | Dokumentace Microsoftu
description: Jak vytvořit obory, které se spustit pracovní postup akce na základě stavu akce skupiny v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: ac184ce790a0700fcacc63f70c2bb321142d7224
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320539"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Spustit akce na základě stavu skupiny pomocí oborů v Azure Logic Apps

Ke spouštění akcí, až po jiné skupiny akcí úspěch nebo neúspěch, skupině tyto akce uvnitř *oboru*. Tato struktura je užitečné, když chcete uspořádání akcí jako logické skupiny, vyhodnocení stavu této skupiny a provádět akce, které jsou založeny na stav oboru. Jakmile skončí všechny akce v oboru, oboru také získá svůj vlastní stav. Například můžete použít obory, pokud chcete implementovat [výjimek a zpracování chyb](../logic-apps/logic-apps-exception-handling.md#scopes). 

Pokud chcete zkontrolovat stav oboru, můžete použít stejných kritérií, které používáte k určení logiku aplikace spustit stav, jako je například "ÚSPĚCH", "Se nezdařilo", "Zrušeno" a tak dále. Ve výchozím nastavení když všechny oboru akce úspěšné, označí stav oboru se "ÚSPĚCH". Ale když selže žádnou akci v oboru, nebo je zrušen, stav oboru je označen "Se nezdařilo." Omezení u oborů najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Tady je například aplikace logiky na základní úrovni, který se používá ke spouštění určitých akcí a podmínku pro kontrolu stavu rozsahu oboru. Pokud všechny akce v rámci selhání nebo neočekávané ukončení, obor je označen "Failed" nebo "Přerušeno" v uvedeném pořadí a aplikace logiky odešle zprávu "Oboru se nezdařilo". Pokud s vymezeným oborem akce úspěšné, aplikace logiky odešle zprávu "Oboru bylo dokončeno".

![Nastavení aktivační události "Plán – opakování"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Požadavky

V příkladu v tomto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* E-mailový účet z libovolného e-mailu poskytovatele podporovaného v Logic Apps. Tento příklad používá Outlook.com. Pokud používáte jiného poskytovatele, zůstává obecný postup stejný, ale vaše uživatelské rozhraní zobrazí různé.

* Klíč služby mapy Bing. Tento klíč získat, najdete v článku <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">získání klíče k mapám Bing</a>.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Vytvoření ukázkové aplikace logiky

Nejprve vytvořte Tato ukázková aplikace logiky tak, aby oboru můžete přidat později:

![Vytvoření ukázkové aplikace logiky](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **plán – opakování** aktivační událost, která kontroluje v intervalu, který zadáte, službu mapy Bing
* A **mapy Bing – získat trasu** akci, která zkontroluje dobu trvání cesty mezi dvěma umístěními
* Podmíněný příkaz, který kontroluje, zda doba trvání cesty překročí zadané doba
* Akce, která vám odešle e-mail této aktuální dobu trvání cesty překročí zadanou dobu

Můžete kdykoli uložit aplikaci logiky, tak často uložte svou práci.

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, pokud jste tak již neučinili. Vytvoření prázdné aplikace logiky

1. Přidat **plán – opakování** aktivační událost s těmito nastaveními: **Interval** = "1" a **frekvence** = "Minute"

   ![Nastavení aktivační události "Plán – opakování"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Vizuálně zjednodušení zobrazení a skrýt podrobnosti o každé akce v návrháři, každá akce tvar sbalení během postupu v těchto kroků.

1. Přidat **mapy Bing – získat trasu** akce. 

   1. Pokud ještě nemáte připojení k mapám Bing, budete vyzváni k vytvoření připojení.

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Název připojení** | PřipojeníMapyBing | Zadejte název připojení. | 
      | **Klíč rozhraní API** | <*klíč-služby-Mapy-Bing*> | Zadejte klíč Map Bing, který jste dříve dostali. | 
      ||||  

   1. Nastavení vašeho **získat trasu** akce, jak je znázorněno v tabulce pod tento obrázek:

      ![Nastavení "Mapy Bing – získat trasu" akce](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Další informace o těchto parametrech najdete v tématu [Výpočet trasy](https://msdn.microsoft.com/library/ff701717.aspx).

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Bod na trase 1** | <*Spuštění*> | Zadejte počátek vaší cesty. | 
      | **Bod na trase 2** | <*ukončení*> | Zadejte cíl vaší trasy. | 
      | **Vyloučit** | Žádný | Zadejte položky, které chcete se na trase vyhnout, jako je například dálnice, mýtné a tak dále. Možné hodnoty najdete v části [výpočet trasy](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimalizovat** | timeWithTraffic | Vyberte parametr k optimalizaci vaší trasy, jako je například vzdálenost, doba aktuální informace o provozu a tak dále. Tento příklad používá tuto hodnotu: "timeWithTraffic" | 
      | **Jednotka vzdálenosti** | <*vaše-volba*> | Zadejte jednotka vzdálenosti použitá k výpočtu vaší trasy. Tento příklad používá tuto hodnotu: "Míli" | 
      | **Způsob cestování** | Autem | Zadejte režim dopravy pro danou trasu. Tento příklad používá tuto hodnotu "Řidičského" | 
      | **Datum a čas přejezdu** | Žádný | Platí pro pouze v režimu přenosu. | 
      | **Typ přenosu typu datum** | Žádný | Platí pro pouze v režimu přenosu. | 
      ||||  

1. [Přidat podmínku](../logic-apps/logic-apps-control-flow-conditional-statement.md) , která zkontroluje, jestli aktuální dobu trvání cesty s provozem překročí určitou dobu. V tomto příkladu postupujte podle těchto kroků:

   1. Přejmenujte podmínku s použitím tohoto popisu: **Pokud doba provozu je více než určený čas**

   1. V levém sloupci klikněte do **zvolit hodnotu** pole, zobrazí se seznam dynamického obsahu. V tomto seznamu, vyberte **doba trvání cesty s provozem** pole, která je v sekundách. 

      ![Vytvoření podmínky](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. V prostředním poli vyberte tento operátor: **je větší než**

   1. Ve sloupci úplně vpravo, zadejte tuto hodnotu porovnání, který se nachází v řádu sekund a equivlent na 10 minut: **600**

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Dokončená podmínka](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. V **při hodnotě true** větve, přidejte akci "Odeslat e-mail" pro vašeho poskytovatele e-mailu. Nastavte tuto akci podle postupu pod tímto obrázkem:

   ![Přidat akci "Odeslat e-mail" k "při hodnotě true" větve](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. V **k** zadejte e-mailovou adresu pro účely testování.

   1. V **subjektu** pole, zadejte následující text:

      ```Time to leave: Traffic more than 10 minutes```

   1. V **tělo** zadejte tento text s koncovou mezerou: 

      ```Travel time: ```

      Zatímco ukazatel myši se zobrazí v **tělo** pole, seznamu dynamického obsahu zůstane otevřený tak, že vyberete všechny parametry, které jsou v tuto chvíli k dispozici.

   1. V seznamu dynamického obsahu vyberte **Výraz**.

   1. Vyhledejte a vyberte **div()** funkce. 
   Umístěte kurzor v dovnitř závorek funkce.

   1. Kurzor je v závorkách funkce, zvolte **dynamický obsah** tak, aby zobrazil seznam dynamického obsahu. 
   
   1. Z **získat trasu** vyberte **provozu doba provozu** pole.

      ![Vyberte "Provozu doba provozu"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Po pole přeloží do formátu JSON, přidejte **čárkou** (```,```) za nímž následuje číslo ```60``` tak, aby se převést hodnotu **provozu doba provozu** ze sekund na minuty. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Výraz se teď bude vypadat jako v tomto příkladu:

      ![Dokončení výrazu](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Jakmile budete hotovi, zvolte **OK**.

  1. Po výraz se přeloží, přidejte tento text s přední místa: ``` minutes```
  
     Vaše **tělo** pole bude vypadat jako v tomto příkladu:

     ![Dokončení pole "Body"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

1. Uložte svou aplikaci logiky.

V dalším kroku přidáte obor, takže můžete seskupit konkrétní akce a vyhodnotit jejich stav.

## <a name="add-a-scope"></a>Přidat obor

1. Pokud jste to ještě neudělali, otevřete v návrháři aplikace logiky aplikace logiky. 

1. Umístění pracovního postupu, který chcete přidáte nový obor. Například chcete-li přidat obor mezi stávající kroky v postupu aplikace logiky, postupujte takto: 

   1. Přesuňte ukazatel myši na šipku, ve které chcete přidat oboru. 
   Zvolte **znaménko plus** (**+**) > **přidat akci**.

      ![Přidat obor](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Do vyhledávacího pole zadejte jako filtr "rozsah". 
   Vyberte **oboru** akce.

## <a name="add-steps-to-scope"></a>Přidání kroků do oboru

1. Nyní přidejte kroky nebo přetáhněte existující kroky, které chcete spustit v oboru. V tomto příkladu přetáhněte do rozsahu těchto akcí:
      
   * **Získat trasu**
   * **Pokud doba provozu je více než určený čas**, což zahrnuje i **true** a **false** větví

   Aplikace logiky je teď vypadá jako v tomto příkladu:

   ![Přidat obor](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. V rámci oboru přidejte podmínku, která kontroluje stav obor. Přejmenujte podmínku s použitím tohoto popisu: **Pokud oboru se nezdařilo**

   ![Přidat podmínku pro kontrolu stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. V podmínce přidejte tyto výrazy, které kontrolují, zda je rovno "Failed" nebo "Přerušeno" stav oboru. 

   1. Chcete-li přidat další řádek, zvolte **přidat**. 

   1. V každém řádku klikněte do levého pole, zobrazí se seznam dynamického obsahu. 
   Ze seznamu dynamického obsahu vyberte **výraz**. Do textového pole zadejte tento výraz a klikněte na tlačítko **OK**: 
   
      `result('Scope')[0]['status']`

      ![Přidat výraz, který kontroluje stav obor](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Pro obě řádky, vyberte **rovná** jako operátor. 
   
   1. Porovnání hodnot, na prvním řádku, zadejte `Failed`. 
   Ve druhém řádku, zadejte `Aborted`. 

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Přidat výraz, který kontroluje stav obor](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Nyní nastavte podmínky `runAfter` vlastnost tak, aby podmínka kontroluje stav oboru a spuštění odpovídající akce, které definujete v dalších krocích.

   1. Na **oboru se nezdařilo-li** podmínky, zvolte **tlačítko se třemi tečkami** (...) a pak zvolte **nakonfigurovat vlastnost runafter**.

      ![Nastavit vlastnost "runAfter.](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Vyberte všechny tyto stavy oboru: **úspěšné**, **selhala**, **se přeskočila**, a **vypršení časového limitu**

      ![Vyberte rozsah stavů](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Jakmile budete hotovi, zvolte **provádí**. 
   Podmínka se teď zobrazuje ikona "informace o".

1. V **při hodnotě true** a **případě hodnoty false** větví, přidání akce, které chcete provést na základě stavu každého rozsahu například odeslat e-mailem nebo zprávy.

   ![Přidání akce, které se provedou v závislosti na rozsahu stav](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Uložte svou aplikaci logiky.

Aplikace logiky dokončená teď vypadá jako v tomto příkladu:

![Hotová aplikace logiky s oborem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Otestovat svou práci

Na panelu nástrojů návrháře zvolte **spustit**. Pokud všechny vymezené akce úspěšné, může se zobrazit zpráva "Oboru bylo dokončeno". Pokud není úspěšné všechny akce s vymezeným oborem, může se zobrazit zpráva "Oboru se nezdařilo". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definice JSON

Pokud pracujete v zobrazení kódu, můžete definovat strukturu oboru v definici JSON vaší aplikace logiky místo. Tady je příklad definici JSON aktivační události a akce v předchozí aplikace logiky:

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
* Odeslání návrhu nebo hlasování o funkcích a návrhy, najdete v tématu [webu zpětné vazby uživatelů Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazů přepínače)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučit paralelními kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
