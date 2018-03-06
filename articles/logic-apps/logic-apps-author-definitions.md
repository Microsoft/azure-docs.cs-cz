---
title: "Sestavení v definicích aplikaci logiky s JSON - Azure Logic Apps | Microsoft Docs"
description: "Přidání parametrů, zpracování řetězců, vytvořit parametr maps a získat data pomocí funkce datum"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 01/31/2018
ms.author: LADocs; estfan
ms.openlocfilehash: d05f7e34cbe670db6733c199e3420c810c304a84
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="build-on-your-logic-app-definition-with-json"></a>Sestavení na svou definici. aplikaci logiky s JSON

Chcete-li provést další pokročilé úlohy s [Azure Logic Apps](../logic-apps/logic-apps-overview.md), zobrazení kódu můžete upravit aplikace definice logiku, která používá jednoduchý a deklarativní jazyk JSON. Pokud jste to ještě neudělali, přečtěte si nejprve [postup vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Další informace naleznete [úplné referenční dokumentace pro jazyk definic workflowů](http://aka.ms/logicappsdocs).

> [!NOTE]
> Některé funkce Azure Logic Apps, jako jsou parametry, jsou k dispozici pouze v případě, že pracujete v zobrazení kódu pro definici aplikace logiky. Parametry umožňují znovu použít hodnoty v celé aplikaci logiky. Pokud chcete použít stejné e-mailovou adresu ve několik akcí, například definujte této e-mailovou adresu jako parametr.

## <a name="view-and-edit-your-logic-app-definitions-in-json"></a>Zobrazit a upravit vaše logiku aplikace definice ve formátu JSON

1. Přihlaste se na web [Azure Portal](https://portal.azure.com "Azure Portal").

2. V levé nabídce zvolte **další služby**. V části **Podniková integrace** zvolte **Logic Apps**. Vyberte svou aplikaci logiky.

3. Z nabídky aplikace logiky v části **nástroje pro vývoj**, zvolte **zobrazení kódu aplikace logiky**.

   Otevře okno zobrazení kódu a zobrazuje svou definici. aplikaci logiky.

## <a name="parameters"></a>Parametry

Parametry umožňují znovu použít hodnoty v celé aplikaci logiky a jsou vhodné pro nahrazení hodnot, které mohou změnit často. Například pokud máte e-mailovou adresu, kterou chcete použít na více místech, měli byste tuto e-mailovou adresu jako parametr. 

Parametry jsou užitečné, i když je nutné přepsat parametry v různých prostředích, další informace o [parametry pro nasazení](#deployment-parameters) a [REST API pro Azure Logic Apps dokumentaci](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parametry jsou dostupné jenom v zobrazení kódu.

V [první aplikaci logiky příklad](../logic-apps/quickstart-create-first-logic-app-workflow.md), jste vytvořili pracovní postup, který odešle e-mailů, jakmile se zobrazí nové příspěvky v informačního kanálu RSS na web. Adresa URL informačního kanálu je pevně zakódované, takže tento příklad ukazuje, jak nahradit hodnotu dotazu s parametrem, abyste snadněji změnit adresu URL informačního kanálu.

1. V zobrazení kódu najdete `parameters : {}` objektu a přidejte `currentFeedUrl` objektu:

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. V `When_a_feed-item_is_published` akci najít `queries` části a nahraďte hodnotu dotazu s `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **Před**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **Po**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   Pro připojení dvě nebo více řetězce, můžete také použít `concat` funkce. 
   Například `"@concat('#',parameters('currentFeedUrl'))"` funguje stejně jako v předchozím příkladu.

3.  Jakmile budete hotoví, vyberte **Uložit**. 

Teď můžete změnit předáním jinou adresu URL prostřednictvím informačního kanálu RSS webu `currentFeedURL` objektu.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parametry nasazení pro různá prostředí

Nasazení životních mají obvykle, prostředí pro vývoj, pracovní a provozní. Například můžete použít stejné definici aplikace logiky v těchto prostředích ale použití různých databází. Podobně můžete chtít použít stejné definice v různých oblastech pro vysokou dostupnost, ale má každá instance aplikace logiky k použití databáze této oblasti. 

> [!NOTE] 
> Tento scénář se liší od trvá parametry v *runtime* kde byste měli používat `trigger()` funkce místo.

Zde je základní definice:

``` json
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
V skutečnou `PUT` požadavku pro logic apps, můžete zadat parametr `uri`. V každém prostředí, můžete zadat jinou hodnotu pro `connection` parametr. Protože výchozí hodnota už existuje, datové části aplikace logiky vyžaduje tento parametr:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Další informace najdete v tématu [REST API pro Azure Logic Apps dokumentaci](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Proces řetězce s funkcí

Služba Logic Apps obsahuje různé funkce pro práci s řetězci. Předpokládejme například, že chcete předat název společnosti z pořadí do jiného systému. Ale nevíte jistě o správné zpracování pro kódování znaků. Může provádět na tento řetězec kódování base64, ale abyste se vyhnuli řídicí sekvence v adrese URL, můžete nahradit několik znaků místo. Navíc stačí pouze dílčí řetězec pro název společnosti vzhledem k tomu, že se nepoužívají prvních 5 znaků. 

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Tyto kroky popisují, jak tento příklad zpracovává tento řetězec z uvnitř funguje na vnější:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Získat [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) pro název společnosti, takže získáte celkový počet znaků.

2. Kratší řetězec získáte odečtena `5`.

3. Teď se [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Spustit v indexu `5`a přejděte na zbytek řetězce.

4. Převést tento dílčí [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) řetězec.

5. Nyní [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) všechny `+` znaků a obsahující `-` znaků.

6. Nakonec [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) všechny `/` znaků a obsahující `_` znaků.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapování položky seznamu hodnot vlastností, potom použijte mapy jako parametry

Chcete-li získat odlišné výsledky na základě vlastnosti na hodnotu, můžete vytvořit mapu, která odpovídá všechny hodnoty vlastností na výsledek a pak použít tento mapování jako parametr. 

Tento pracovní postup například definuje některé kategorie jako parametry a mapu, která odpovídá těchto kategorií s konkrétní adresy URL. Pracovní postup nejdřív získá seznam článků. Potom pracovní postup používá mapy Pokud chcete vyhledat adresu URL odpovídající kategorii jednotlivých článků.

*   [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) Funkce kontroluje, zda kategorii odpovídá známé definované kategorie.

*   Po získání odpovídající kategorii, vrátí příklad položky od mapy pomocí hranatými závorkami: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Získání dat pomocí funkce datum

Chcete-li získat data ze zdroje dat, která nenabízí nativní podporu *aktivační události*, můžete použít datum funkce pro práci s časy a místo toho data. Například tento výraz najde, jak dlouho kroky tento pracovní postup trvá, z uvnitř funguje na vnější:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` akce, extrakce `startTime`. 
2. Získat aktuální čas s `utcNow()`.
3. Odečtena sekundu:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Můžete použít jiné jednotky doby, jako je třeba `minutes` nebo `hours`. 

3. Teď můžete porovnat tyto dvě hodnoty. 

   Pokud první hodnota je menší než druhá hodnota, která pak více než jedna sekunda byla úspěšná, protože byl nejprve umístit pořadí.

K formátování kalendářních dat, můžete použít formátování řetězce. Například RFC1123 získáte pomocí [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Další informace o [datum formátování](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazech switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (smyčky)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (oborům)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Další informace o [jazyk definic workflowů schéma pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Další informace o [akce pracovního postupu a aktivačních událostí pro Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)