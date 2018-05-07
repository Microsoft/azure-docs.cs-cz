---
title: Vytvořit, upravit nebo rozšířit JSON pro logiku aplikace definice - Azure Logic Apps | Microsoft Docs
description: Vytváření a přizpůsobení definice aplikace logiky ve formátu JSON
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: f37f600d001b110775d8ca0e78950e3b8743df82
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>Vytvořit, upravit nebo přizpůsobit JSON definice aplikace logiky

Při vytváření podnikové řešení integrace s automatizované pracovní postupy v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), základní definice aplikace logiky použít jednoduchý a deklarativní JSON JavaScript Object Notation () spolu s [ Pracovní postup definice jazyka splní schématu](../logic-apps/logic-apps-workflow-definition-language.md) pro jejich popis a ověření. Tyto formáty usnadňují logiku aplikace definice ke čtení a pochopit, aniž by věděly mnohem o kódu. Pokud chcete automatizovat vytváření a nasazování aplikací logiky, můžete zahrnout definice logiku aplikace jako [prostředky Azure](../azure-resource-manager/resource-group-overview.md) uvnitř [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment). Vytvořit, spravovat a nasazovat aplikace logiky, pak můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), nebo [rozhraní API REST Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Pro práci s definice aplikace logiky ve formátu JSON, otevřete editor zobrazení kódu při práci na portálu Azure nebo v sadě Visual Studio, nebo zkopírujte do libovolného editoru, který chcete definici. Pokud jste nové aplikace logiky, přečtěte si [postup vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Některé funkce Azure Logic Apps, například definování parametry a více aktivačních událostí v definicích aplikace logiky, jsou k dispozici pouze ve formátu JSON, návrháře typu aplikace logiky. Takže k těmto úlohám je možné v zobrazení kódu nebo jiný editor.

## <a name="edit-json---azure-portal"></a>Upravit JSON - portálu Azure

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">portálu Azure</a>.

2. V levé nabídce zvolte **všechny služby**. Do vyhledávacího pole Najít "aplikace logiky" a potom ve výsledcích vyberte svou aplikaci logiky.

3. V nabídce aplikace logiky v části **nástroje pro vývoj**, vyberte **zobrazení kódu aplikace logiky**.

   Editor kódu zobrazení otevře a zobrazuje svou definici. aplikaci logiky ve formátu JSON.

## <a name="edit-json---visual-studio"></a>Upravit JSON - sady Visual Studio

Než začnete pracovat na svou definici. aplikaci logiky v sadě Visual Studio, ujistěte se, že jste [nainstalovány nástroje pro požadované](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). K vytvoření aplikace logiky pomocí sady Visual Studio, zkontrolujte [rychlý start: automatizaci úloh a procesů službou Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

V sadě Visual Studio můžete otevřít logiku aplikace, které byly vytvořeny a nasadit buď přímo z portálu Azure, nebo jako projekty Azure Resource Manageru ze sady Visual Studio.

1. Otevřete řešení sady Visual Studio nebo [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) projektu, který obsahuje aplikace logiky.

2. Vyberte a otevřete definici aplikace logiky, která ve výchozím nastavení, zobrazí se v [šablony Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment)s názvem **LogicApp.json**. Můžete používat a přizpůsobit této šablony pro nasazení do různých prostředích.

3. Otevřete místní nabídku pro definici aplikace logiky a šablonu. Vyberte **Otevřít pomocí Návrháře aplikace logiky**.

   ![Otevřete logiku aplikace v řešení sady Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. V dolní části návrháře, zvolte **zobrazení kódu**. 

   Editor kódu zobrazení otevře a zobrazuje svou definici. aplikaci logiky ve formátu JSON.

5. Se vrátíte do návrháře zobrazení, v dolní části editoru kódu zobrazení, zvolte **návrhu**.

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
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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