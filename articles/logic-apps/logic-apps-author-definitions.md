---
title: Vytvořit, upravit nebo rozšířit JSON pro logiku definic aplikací – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvářet a rozšiřovat JSON pro logiku definic aplikací v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 1f2e136810194ad044255f9d129b5c03549221b9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128656"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Vytvořit, upravit nebo rozšířit JSON pro logiku definic aplikací v Azure Logic Apps

Při vytváření podnikových řešení pro integraci s automatizované pracovní postupy v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), základní definic aplikací logiky použít jednoduchý a deklarativní notace JSON (JavaScript Object) spolu s [ Schéma definici jazyka (splní) pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md) jejich popis a ověřování. Tyto formáty snadněji logiky definic aplikací ke čtení a pochopení bez znalosti většinu kódu. Pokud chcete automatizovat vytváření a nasazování aplikací logic apps, můžete zahrnout definic aplikací logiky jako [prostředky Azure](../azure-resource-manager/resource-group-overview.md) uvnitř [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment). Chcete-li vytvořit, spravovat a nasazovat aplikace logiky pak můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), nebo [REST API služby Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Pro práci s definic aplikací logiky ve formátu JSON, otevřete editor zobrazení kódu při práci na webu Azure Portal nebo v sadě Visual Studio nebo zkopírování definice do libovolného editoru, který chcete. Pokud se službou logic Apps teprve začínáte, přečtěte si [vytvoření vaší první aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Některé funkce Azure Logic Apps, jako je například definovat parametry a více aktivačních událostí v definic aplikací logiky, jsou k dispozici pouze ve formátu JSON, není návrháři pro Logic Apps. Abyste pro tyto úlohy, musí pracovat v zobrazení kódu nebo jiného editoru.

## <a name="edit-json---azure-portal"></a>Upravit JSON - webu Azure portal

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">portálu Azure</a>.

2. V levé nabídce zvolte **všechny služby**. Do vyhledávacího pole vyhledejte "aplikace logiky" a potom ve výsledcích vyberte svou aplikaci logiky.

3. V nabídce aplikace logiky podle **nástroje pro vývoj**vyberte **zobrazení kódu aplikace logiky**.

   Editor zobrazení kódu se otevře a zobrazí definici aplikace logiky ve formátu JSON.

## <a name="edit-json---visual-studio"></a>Upravit JSON – Visual Studio

Než můžete pracovat na definici aplikace logiky v sadě Visual Studio, ujistěte se, že jste [nainstalovány nástroje požadované](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Vytvoření aplikace logiky pomocí sady Visual Studio, najdete v tématu [rychlý start: automatizace úloh a procesů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

V sadě Visual Studio můžete otevřít aplikace logiky, které byly vytvořeny a nasazeny buď přímo z portálu Azure portal, nebo jako projekty Azure Resource Manageru ze sady Visual Studio.

1. Otevřít řešení sady Visual Studio nebo [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) projektu, který obsahuje vaši aplikaci logiky.

2. Vyhledání a otevření definice aplikace logiky, která ve výchozím nastavení, zobrazí se v [šablony Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment)s názvem **LogicApp.json**. Můžete použít a přizpůsobit této šablony pro nasazení do různých prostředí.

3. Otevřete místní nabídku pro definici aplikace logiky a šablony. Vyberte **Otevřít pomocí Návrháře aplikace logiky**.

   ![Aplikace logiky otevřít v řešení sady Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. V dolní části návrháře zvolte **zobrazení kódu**. 

   Editor zobrazení kódu se otevře a zobrazí definici aplikace logiky ve formátu JSON.

5. Se vraťte do návrháře zobrazení, v dolní části editoru zobrazení kódu zvolte **návrhu**.

## <a name="parameters"></a>Parametry

Parametry umožňují opakované použití hodnot v rámci vaší aplikace logiky a jsou vhodné pro nahrazení hodnoty, které může často měnit. Například pokud máte e-mailovou adresu, kterou chcete použít na více místech, byste měli definovat tuto e-mailovou adresu jako parametr. 

Parametry jsou užitečné také, pokud je nutné přepsat parametry v různých prostředích, další informace o [parametry pro nasazení](#deployment-parameters) a [rozhraní REST API pro Azure Logic Apps dokumentaci](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parametry jsou dostupné jenom v zobrazení kódu.

V [první příklad aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md), jste vytvořili pracovní postup, který se odešle e-mailů, když se zobrazí nové příspěvky v informačním kanálu RSS webu. Adresa URL informačního kanálu je pevně zakódované, takže tento příklad ukazuje, jak nahradit hodnotu dotazu s parametrem tak, aby adresa URL informačního kanálu můžete změnit snadněji.

1. V zobrazení kódu vyhledejte `parameters : {}` objektu a přidejte `currentFeedUrl` objektu:

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

   Pro připojení dvou nebo více řetězců, můžete použít také `concat` funkce. 
   Například `"@concat('#',parameters('currentFeedUrl'))"` funguje stejně jako v předchozím příkladu.

3.  Jakmile budete hotoví, vyberte **Uložit**. 

Teď můžete změnit na webu kanálu RSS předáním jinou adresu URL prostřednictvím `currentFeedURL` objektu.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parametry nasazení pro různá prostředí

Životní cyklus nasazení mají obvykle, prostředí pro vývoj, přípravném nebo produkčním prostředí. Může například používat stejnou definici aplikace logiky v těchto prostředích ale použití různých databází. Podobně můžete chtít použít stejnou definici pojmů v různých oblastech pro zajištění vysoké dostupnosti, ale má každé instanci aplikace logiky k použití databáze tuto oblast. 

> [!NOTE] 
> Tento scénář se liší od pořízení parametry na *runtime* kam byste měli použít `trigger()` namísto toho funkci.

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
Ve skutečných `PUT` žádosti pro logic apps, můžete zadat parametr `uri`. V každé prostředí, můžete zadat jinou hodnotu pro `connection` parametru. Protože výchozí hodnota již existuje, tento parametr vyžaduje datové části aplikace logiky:

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

Další informace najdete v tématu [rozhraní REST API pro Azure Logic Apps dokumentaci](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Proces řetězce s využitím functions

Logic Apps se různé funkce pro práci s řetězci. Předpokládejme například, že chcete předat název společnosti z objednávky do jiného systému. Však nevíte o správné zpracování pro kódování znaků. Můžete provést na tento řetězec kódování base64, ale aby se zabránilo řídicí sekvence v adrese URL můžete nahradit několik znaků místo. Navíc potřebujete pouze dílčí řetězec pro název společnosti vzhledem k tomu, že nejsou použity prvních 5 znaků. 

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

Tyto kroky popisují, jak v tomto příkladu zpracovává tento řetězec zevnitř funguje na vnější:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Získejte [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) název společnosti, tak získáte celkový počet znaků.

2. Kratší řetězec získáte odečtení `5`.

3. Získejte teď [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Spustit v indexu `5`a přejděte na zbytek řetězce.

4. Převést tento dílčí [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) řetězec.

5. Nyní [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) všechny `+` znaků a obsahující `-` znaků.

6. Nakonec [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) všechny `/` znaků a obsahující `_` znaků.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapování hodnot vlastností položky seznamu, a pak pomocí mapy jako parametry

Chcete-li získat jiné výsledky na základě hodnoty vlastnosti, můžete vytvořit mapu, která odpovídá všechny hodnoty vlastností na výsledek a pak použít jako parametr, která je namapována. 

Například tento pracovní postup definuje některé kategorie jako parametry a mapu, která odpovídá kategorie s konkrétní adresy URL. Nejprve pracovní postup získává seznam článků. Pracovní postup potom použije na mapě Pokud chcete vyhledat adresu URL odpovídající kategorii pro každý článek.

*   [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) Funkce zkontroluje, zda odpovídá kategorii známé definovaných kategorií.

*   V příkladu po získání odpovídající kategorii, načítá položku z mapy pomocí hranatých závorek: `parameters[...]`

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

## <a name="get-data-with-date-functions"></a>Získání dat pomocí datové funkce

K získání dat ze zdroje dat, která nenabízí nativní podporu *triggery*, můžete použít data funkce pro práci s časem a místo toho data. Například tento výraz najde, jak dlouho je možné tento pracovní postup, pracujete z vnitřního ven:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Z `order` akce, extrahovat `startTime`. 
2. Získat aktuální čas s `utcNow()`.
3. Odečtení jedné sekundy:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Můžete použít jiné jednotky doby, jako je třeba `minutes` nebo `hours`. 

3. Teď můžete porovnat tyto dvě hodnoty. 

   Pokud je první hodnota menší než druhá hodnota, která pak více než jedna sekunda byla úspěšná, protože byla objednávka zadána první.

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
* [Spustit kroky na základě různých hodnot (příkazů přepínače)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučit paralelními kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Další informace o [schéma jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Další informace o [pracovních postupů akcí a triggerů pro Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)