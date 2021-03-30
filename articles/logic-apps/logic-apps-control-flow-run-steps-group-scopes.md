---
title: Seskupit a spustit akce podle oboru
description: Vytvoření vymezených akcí, které se spouštějí na základě stavu skupiny v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92314841"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Spouštění akcí na základě stavu skupiny pomocí oborů v Azure Logic Apps

Chcete-li spouštět akce až po úspěšném nebo neúspěšném provedení jiné skupiny akcí, seskupte tyto akce do *oboru*. Tato struktura je užitečná v případě, že chcete uspořádat akce jako logickou skupinu, vyhodnotit stav této skupiny a provést akce, které jsou založeny na stavu oboru. Po dokončení všech akcí v oboru, který je spuštěn, rozsah také získá svůj stav. Můžete například použít obory, pokud chcete implementovat [výjimku a zpracování chyb](../logic-apps/logic-apps-exception-handling.md#scopes). 

Chcete-li zjistit stav oboru, můžete použít stejná kritéria, která použijete k určení stavu spuštění aplikace logiky, jako je například **úspěšné**, **neúspěšné**, **zrušené** a tak dále. Ve výchozím nastavení se po úspěšném dokončení všech akcí oboru stav oboru označí jako **úspěšný**. Pokud ale dojde k selhání jakékoli akce v oboru nebo zrušení, stav tohoto oboru se označí jako **neúspěšné**. Omezení pro rozsahy najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Tady je například aplikace logiky vysoké úrovně, která používá obor ke spouštění specifických akcí a podmínku pro kontrolu stavu oboru. Pokud některé akce v oboru selžou nebo neočekávaně skončí, je obor označený jako **neúspěšný** nebo **přerušený** a aplikace logiky pošle zprávu "obor se nepovedlo". Pokud všechny akce v oboru proběhnou úspěšně, aplikace logiky pošle zprávu "obor byl úspěšný".

![Diagram znázorňuje tok oboru aplikace logiky s příklady "obor se selháním" a "obor byl úspěšný".](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle příkladu v tomto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* E-mailový účet od jakéhokoli poskytovatele e-mailu, který podporuje služba Logic Apps. V tomto příkladu se používá Outlook.com. Pokud používáte jiného poskytovatele, zůstane obecný tok stejný, ale vaše uživatelské rozhraní se zobrazí jinak.

* Klíč mapy Bing. Pokud chcete získat tento klíč, přečtěte si téma <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">získání klíče mapy Bing</a>.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Vytvoření ukázkové aplikace logiky

Nejdřív vytvořte tuto ukázkovou aplikaci logiky, abyste později mohli přidat rozsah:

![Vytvoření ukázkové aplikace logiky](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Trigger **opakování plánu** , který kontroluje službu Bing Maps v intervalu, který zadáte
* Akce **mapy Bing – získat trasu** , která kontroluje dobu trvání cesty mezi dvěma místy
* Podmíněný příkaz, který kontroluje, jestli doba trvání cesty překročí zadanou dobu trvání cesty
* Akce, která vás pošle e-mailem, že aktuální doba trvání cesty překročí zadaný čas

Aplikaci logiky můžete kdykoli uložit, takže svou práci budete často ukládat.

1. Pokud jste to ještě neudělali, přihlaste se k <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Vytvoření prázdné aplikace logiky

1. Přidejte aktivační událost **plán-opakování** s těmito nastaveními: **interval** = "1" a **frekvence** = "minuta"

   ![Nastavení triggeru "plán-opakování"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Chcete-li vizuálně zjednodušit zobrazení a skrýt podrobnosti o jednotlivých akcích v návrháři, sbalte v rámci těchto kroků tvar jednotlivých akcí.

1. Přidejte akci **mapy Bing – získat trasu** . 

   1. Pokud ještě nemáte připojení ke službě Mapy Bing, budete požádáni o vytvoření připojení.

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Název připojení** | PřipojeníMapyBing | Zadejte název připojení. | 
      | **Klíč rozhraní API** | <*váš klíč-Bing-Maps-Key*> | Zadejte klíč Map Bing, který jste dříve dostali. | 
      ||||  

   1. Nastavte akci **získat trasu** , jak je znázorněno v tabulce pod tímto obrázkem:

      ![Nastavení akce mapy Bing – získat trasu](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Další informace o těchto parametrech najdete v tématu [Výpočet trasy](/bingmaps/rest-services/routes/calculate-a-route).

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Bod na trase 1** | <*Čína*> | Zadejte původ trasy. | 
      | **Bod na trase 2** | <*účelu*> | Zadejte cíl trasy. | 
      | **Vyhnutí se** | Žádné | Zadejte položky, které vám zabrání v trasách, jako jsou například dálnice, mýtné atd. Možné hodnoty najdete v tématu [Výpočet trasy](/bingmaps/rest-services/routes/calculate-a-route). | 
      | **Zvýšit** | timeWithTraffic | Vyberte parametr pro optimalizaci trasy, například vzdálenost, čas s aktuálními informacemi o provozu a tak dále. V tomto příkladu se používá tato hodnota: "timeWithTraffic" | 
      | **Jednotka vzdálenosti** | <*vaše preference*> | Zadejte jednotku ke vzdálenosti pro výpočet trasy. V tomto příkladu se používá tato hodnota: km. | 
      | **Způsob cestování** | Autem | Zadejte režim cesty pro trasu. V tomto příkladu se používá tato hodnota "jízda". | 
      | **Datum a čas přejezdu** | Žádné | Platí jenom pro režim přenosu. | 
      | **Typ Date-Type přenosu** | Žádné | Platí jenom pro režim přenosu. | 
      ||||  

1. [Přidejte podmínku](../logic-apps/logic-apps-control-flow-conditional-statement.md) , která zkontroluje, jestli aktuální doba trvání cesty s provozem přesáhne zadaný čas. 
   V tomto příkladu postupujte podle těchto kroků:

   1. Přejmenujte podmínku s použitím tohoto popisu: **Pokud je doba provozu delší než zadaný čas** .

   1. Ve sloupci úplně vlevo klikněte do pole **zvolit hodnotu** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte pole **provoz doby trvání cesty** , což je v sekundách. 

      ![Vytvoření podmínky](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. V prostředním poli vyberte Tento operátor: **je větší než**

   1. Do sloupce napravo vpravo zadejte tuto hodnotu pro porovnání, která je v sekundách a odpovídá 10 minutám: **600**

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Dokončená podmínka](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Ve větvi **Pokud je true** přidejte pro poskytovatele e-mailu akci "Odeslat e-mail". 
   Tuto akci nastavte pomocí následujících kroků v této imagi:

   ![Přidat akci "Odeslat e-mail" do větve "Pokud je true"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Do pole **do** zadejte svou e-mailovou adresu pro účely testování.

   1. Do pole **subjekt** zadejte tento text:

      ```Time to leave: Traffic more than 10 minutes```

   1. **Do pole text** zadejte text s koncovým mezerou: 

      ```Travel time:```

      I když se kurzor zobrazí v poli **tělo** , zůstane seznam dynamického obsahu otevřený, takže můžete vybrat všechny parametry, které jsou v tomto okamžiku k dispozici.

   1. V seznamu dynamického obsahu vyberte **Výraz**.

   1. Vyhledejte a vyberte funkci **div ()** . 
      Umístěte kurzor dovnitř závorek funkce.

   1. Zatímco se kurzor nachází uvnitř závorek funkce, vyberte **dynamický obsah** , aby se zobrazil seznam dynamického obsahu. 
   
   1. V části **získat trasu** vyberte pole **provoz doby trvání provozu** .

      ![Vyberte provoz trvání provozu.](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Až se pole přeloží na formát JSON, přidejte **čárku** ( ```,``` ) následovaný číslem, ```60``` abyste převedli hodnotu v provozu provozního **provozu** z sekund na minuty. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Váš výraz teď vypadá jako v tomto příkladu:

      ![Výraz dokončení](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Po dokončení vyberte **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Po vyřešení výrazu přidejte tento text do úvodního prostoru: ``` minutes```
  
       Vaše pole **tělo** teď vypadá jako v tomto příkladu:

       ![Bylo dokončeno pole "tělo"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Uložte aplikaci logiky.

Dále přidejte obor, abyste mohli seskupit konkrétní akce a vyhodnotit jejich stav.

## <a name="add-a-scope"></a>Přidání oboru

1. Pokud jste to ještě neudělali, otevřete aplikaci logiky v návrháři aplikace logiky. 

1. Přidejte obor do umístění pracovního postupu, které chcete. Pokud například chcete přidat rozsah mezi existujícími kroky v pracovním postupu aplikace logiky, postupujte takto: 

   1. Přesuňte ukazatel myši na šipku, kam chcete přidat obor. 
   Vyberte znaménko **plus** ( **+** ) > **přidat akci**.

      ![Přidání oboru](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Do vyhledávacího pole zadejte jako filtr "obor". 
   Vyberte akci **oboru** .

## <a name="add-steps-to-scope"></a>Přidání kroků do oboru

1. Nyní přidejte kroky nebo přetáhněte existující kroky, které chcete spustit v rámci oboru. V tomto příkladu přetáhněte tyto akce do oboru:
      
   * **Získat trasu**
   * **Pokud je doba provozu delší než zadaný čas**, který zahrnuje i větve **true** i **false**

   Vaše aplikace logiky teď vypadá jako v tomto příkladu:

   ![Rozsah přidán](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Do oboru přidejte podmínku, která kontroluje stav oboru. Přejmenujte podmínku s použitím tohoto popisu: **Pokud se obor nezdařil** .

   ![Přidat podmínku pro kontrolu stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. V podmínce přidejte tyto výrazy, které zkontrolují, jestli je stav tohoto oboru roven "Failed" nebo "Aborted". 

   1. Chcete-li přidat další řádek, klikněte na tlačítko **Přidat**. 

   1. V každém řádku klikněte do levého pole, aby se zobrazil seznam dynamického obsahu. 
   V seznamu dynamický obsah vyberte možnost **výraz**. Do pole pro úpravy zadejte tento výraz a pak zvolte **OK**: 
   
      `result('Scope')[0]['status']`

      ![Snímek obrazovky, který zobrazuje pole výrazu se zvýrazněným výrazem výsledku](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Pro oba řádky vyberte **je rovno** jako operátor. 
   
   1. Pro hodnoty porovnání zadejte do prvního řádku `Failed` . 
   Do druhého řádku zadejte `Aborted` . 

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Přidat výraz, který kontroluje stav oboru](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Nyní nastavte vlastnost podmínky tak, `runAfter` aby podmínka vyhledá stav oboru a spustí odpovídající akci, kterou definujete v pozdějších krocích.

   1. V **případě selhání oboru if** klikněte na tlačítko se **třemi tečkami** (...) a pak zvolte **Konfigurovat spustit po**.

      ![Konfigurace vlastnosti ' Runafter šablonové '](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Vybrat všechny tyto stavy oboru: **úspěšné**, **neúspěšné**, **přeskočení** a vypršel **časový limit** .

      ![Výběr stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Jakmile budete hotovi, zvolte **Done** (Hotovo). 
   Podmínka nyní zobrazuje ikonu "informace".

1. V části **if true** a v **případě nepravdivých** větví přidejte akce, které chcete provést, na základě každého stavu oboru, například odeslat e-mail nebo zprávu.

   ![Přidat akce, které se mají provést na základě stavu oboru](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Uložte aplikaci logiky.

Vaše dokončená aplikace logiky teď vypadá jako v tomto příkladu:

![Dokončená aplikace logiky s oborem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testování práce

Na panelu nástrojů návrháře klikněte na tlačítko **Spustit**. Pokud jsou všechny akce s oborem úspěšné, zobrazí se zpráva "obor byl úspěšný". Pokud některé akce s oborem nejsou úspěšné, zobrazí se zpráva "obor se nepovedlo". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definice JSON

Pokud pracujete v zobrazení kódu, můžete místo toho definovat strukturu oboru v definici JSON vaší aplikace logiky. Tady je příklad definice JSON pro triggery a akce v předchozí aplikaci logiky:

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

## <a name="get-support"></a>Získání podpory

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Chcete-li odesílat nebo hlasovat o funkcích a návrzích, navštivte [web Azure Logic Apps pro zpětnou vazbu uživatelů](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy Switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit nebo sloučit paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)