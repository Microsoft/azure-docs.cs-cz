---
title: Vytváření proměnných pro ukládání hodnoty - Azure Logic Apps | Microsoft Docs
description: Uložení a správě hodnoty tak, že vytvoříte proměnné v Azure Logic Apps
services: logic-apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: cc464edf416a2b036d84e65e05810104d2706041
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654850"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Vytváření proměnných pro ukládání a správě hodnot v Azure Logic Apps

Tento článek ukazuje, jak můžete ukládat a pracovat s hodnotami v celé aplikaci logiky vytvořením proměnné. Například proměnné vám může pomoct počet pokusů, které spouští smyčku. Když iterování přes pole nebo kontrola pole pro konkrétní položku, můžete použít proměnné tak, aby odkazovaly číslo indexu pro každou položku pole. 

Můžete vytvořit proměnných pro datové typy, jako je například celé číslo, float, logická hodnota, string, pole a objektu. Po vytvoření proměnné, můžete provádět další úlohy, například:

* Získat nebo zadejte odkaz hodnota proměnné.
* Zvýšení nebo snížení proměnnou podle konstantní hodnoty, také známé jako *přírůstek* a *snížení*.
* Přiřadíte jinou hodnotu proměnné.
* Vložení nebo *připojit* hodnota proměnné jako naposledy v řetězec nebo pole.

Proměnné existují a jsou globální pouze v rámci instanci aplikace logiky, která je vytvořila. Navíc se zachová napříč všechny iterace smyčky uvnitř instance aplikace logiky. Při odkazování na proměnnou, použijte název proměnné jako token, nikoli název akce, který je obvykle způsob, jak odkazovat akce výstupy.

Pokud nemáte předplatné Azure ještě <a href="https://azure.microsoft.com/free/" target="_blank">si zaregistrovat bezplatný účet Azure</a>. 

## <a name="prerequisites"></a>Požadavky

Postup popsaný v tomto článku, zde jsou položky, které budete potřebovat:

* Aplikace logiky, kde chcete vytvořit proměnnou 

  Pokud jste nové aplikace logiky, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok v aplikaci logiky 

  Než budete moct přidat akce pro vytváření a práci s proměnnými, musí aplikace logiky začínat aktivační událost.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializovat proměnnou

Můžete vytvořit proměnnou a deklarovat jeho datový typ a počáteční hodnota – vše v rámci jedné akce v aplikaci logiky. Můžete pouze deklarovat proměnné na globální úrovni, není v rámci oborů, podmínky a smyčky. 

1. V <a href="https://portal.azure.com" target="_blank">portál Azure</a> nebo Visual Studio, otevřete aplikaci logiky v návrháři aplikace logiky. 

   Tento příklad používá portál Azure a aplikaci logiky s existující aktivační události.

2. V aplikaci logiky v kroku, na které chcete přidat proměnnou použijte jednu z těchto kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Akce pro přidání](./media/logic-apps-create-variables-store-values/add-action.png)

   * Pokud chcete přidat akci mezi kroky, přesunutím ukazatele myši nad šipku připojování, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a zvolte **přidat akci**.

3. Do vyhledávacího pole zadejte "proměnné" jako filtr. Vyberte ze seznamu akce **proměnné - inicializovat proměnné**.

   ![Vyberte akci](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Zadejte tyto informace pro vaše proměnná:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | Název | Ano | <*Název proměnné*> | Název proměnné se zvýší | 
   | Typ | Ano | <*Typ proměnné*> | Datový typ pro proměnnou | 
   | Hodnota | Ne | <*Počáteční hodnota*> | Počáteční hodnota proměnné <p><p>**Tip**: i když je volitelné, tuto hodnotu nastavit jako osvědčený postup, je neznáte počáteční hodnotu pro vaše proměnná. | 
   ||||| 

   ![Inicializovat proměnnou](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Teď pokračujte v přidávání akce, které chcete. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**.

Pokud přepnete na zobrazení editoru kódu z návrháře, tady je způsob **inicializovat proměnná** akce se zobrazí uvnitř vaší definici aplikace logiky, která je ve formátu JavaScript Object Notation (JSON):

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Zde jsou příklady pro některé typy proměnných:

*Řetězec proměnné*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Logická hodnota proměnné*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Pole s celými čísly*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Pole s řetězci*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Získat hodnota proměnné

Načtení nebo odkaz proměnnou obsah, můžete také použít [variables() funkce](../logic-apps/workflow-definition-language-functions-reference.md#variables) v návrháři aplikace logiky a zobrazení editoru kódu.
Při odkazování na proměnnou, použijte název proměnné jako token, nikoli název akce, který je obvykle způsob, jak odkazovat akce výstupy. 

Například tento výraz získá položky z proměnné pole [vytvořili dříve v tomto článku](#append-value) pomocí **variables()** funkce. **String()** funkce vrátí proměnné obsah ve formátu řetězce: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zvýšení hodnoty proměnné 

Ke zvýšení nebo *přírůstek* proměnné podle konstantní hodnoty, přidejte **proměnné - přírůstek proměnné** akce do aplikace logiky. Tuto akci lze použít pouze proměnné celé číslo a float.

1. V návrháři aplikace logiky, vyberte v části krok, kam chcete zvýšit existující proměnnou, **nový krok** > **přidat akci**. 

   Tato aplikace logiky například již má aktivační událost a akce, která vytvoření proměnné. Ano přidáte nové akce podle těchto kroků:

   ![Akce pro přidání](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Pokud chcete přidat akci mezi existující kroky, přesunutím ukazatele myši nad šipku připojování tak, aby se zobrazí na symbol plus (+). Vyberte znaménko plus a zvolte **přidat akci**.

2. Do vyhledávacího pole zadejte "přírůstek proměnné" jako filtr. Vyberte v seznamu akcí **proměnné - přírůstek proměnné**.

   ![Vyberte akci "Přírůstek proměnné"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Zadejte tyto informace pro zvyšování vaše proměnná:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | Název | Ano | <*Název proměnné*> | Název proměnné se zvýší | 
   | Hodnota | Ne | <*Hodnota přírůstku*> | Hodnota určená pro zvyšování proměnnou. Výchozí hodnota je 1. <p><p>**Tip**: i když je volitelné, tuto hodnotu nastavit jako osvědčený postup abyste vždycky věděli, konkrétní hodnota zvyšování vaše proměnná. | 
   |||| 

   Příklad: 
   
   ![Příklad hodnoty přírůstku](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**. 

Pokud přepnete na zobrazení editoru kódu z návrháře, zde je způsob, jakým **přírůstek proměnné** akce se zobrazí uvnitř vaší definice aplikace logiky, která je ve formátu JSON:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Příklad: Vytvoření čítače smyčky

Proměnné se běžně používají pro počítání počet pokusů, které spouští smyčku. Tento příklad ukazuje, jak vytvořit a použít proměnné pro tuto úlohu vytvořením smyčku, která vrátí počet příloh e-mailem.

1. Na portálu Azure vytvořte aplikaci logiky prázdné. Přidejte aktivační událost, která vyhledává nových e-mailů a všechny přílohy. 

   Tento příklad používá Office 365 Outlook aktivační událost pro **při doručení nových e-mailů**. 
   Chcete-li provést jenom v případě, že má přílohy e-mailu můžete nastavit této aktivační události.
   Všechny konektory, která kontroluje však můžete použít pro nové e-mailů s přílohami, jako je Outlook.com konektor.

2. V aktivační události, zvolte **zobrazit rozšířené možnosti**. Chcete-li aktivační událost kontrolovat přílohy a předávat tyto přílohy do aplikace logiky pracovního postupu, vyberte **Ano** pro tyto vlastnosti:
   
   * **Má přílohu** 
   * **Zahrnout přílohy** 

   ![Zkontrolovat a zahrnout přílohy](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Přidat [ **inicializovat proměnná** akce](#create-variable). Vytvořte proměnnou celé číslo s názvem **počet** s nulou počáteční hodnota.

   ![Přidat akci "Inicializovat proměnné"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Chcete-li procházet každé přílohy, přidejte *pro každou* smyčky výběrem **nový krok** > **Další** > **přidat pro každou**.

   ![Přidání smyčky "pro každou"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. Ve smyčce, klikněte na tlačítko uvnitř **vyberte výstup z předchozích kroků** pole. Jakmile se zobrazí dynamického obsahu seznamu, vyberte **přílohy**. 

   ![Výběr možnosti „Přílohy“](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Přílohy** pole předá pole, které má přílohy e-mailu z výstupu aktivační události do vašeho smyčky.

6. Ve smyčce "pro každou" vyberte **přidat akci**. 

   ![Vyberte možnost "Přidat akci."](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. Do vyhledávacího pole zadejte "přírůstek proměnné" jako filtr. Vyberte ze seznamu akce **proměnné - přírůstek proměnné**.

   > [!NOTE]
   > Zajistěte, aby **přírůstek proměnné** akce se zobrazí uvnitř smyčky. Pokud se zobrazí akce mimo smyčky, přetáhněte akci do smyčky.

8. V **přírůstek proměnné** akce, z **název** seznamu, vyberte **počet** proměnné. 

   ![Vyberte proměnnou "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. V části smyčky přidejte všechny akce, která vám pošle počet příloh. V akci, zahrnují hodnotu z **počet** proměnných, například: 

   ![Přidat akci, která odesílá výsledky](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud svou aplikaci logiky není povolena, v nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů vyberte **povolit**. 

2. Na panelu nástrojů návrháře aplikace logiky, vyberte **spustit**. Tento krok ručně spustí aplikaci logiky.

3. Odešlete e-mail s minimálně jednu přílohu k e-mailový účet, který jste použili v tomto příkladu.

   Tento krok aktivuje aktivační událostí aplikace logiky, která vytvoří a spustí instanci pro svou aplikaci logiky pracovního postupu.
   V důsledku toho aplikaci logiky vám pošle zprávu nebo e-mailu, který ukazuje počet příloh e-mailu, které jste poslali.

Pokud přepnete na zobrazení editoru kódu z návrháře, tady je způsob smyčka "pro každou" se zobrazí se **přírůstek proměnné** akce uvnitř vaší definice aplikace logiky, která je ve formátu JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Dekrementuje proměnnou.

Snížení nebo *snížení* proměnné podle konstantní hodnoty, postupujte podle kroků pro [zvýšení proměnné](#increment-value) s tím rozdílem, že najděte a vyberte **proměnné - snížení proměnná**akce místo. Tuto akci lze použít pouze proměnné celé číslo a float.

Tady jsou vlastnosti **snížení proměnná** akce:

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| Název | Ano | <*Název proměnné*> | Název proměnné se sníží | 
| Hodnota | Ne | <*Hodnota přírůstku*> | Hodnota dekrementace proměnnou. Výchozí hodnota je 1. <p><p>**Tip**: i když je volitelné, tuto hodnotu nastavit jako osvědčený postup abyste vždycky věděli, konkrétní hodnota dekrementace vaše proměnná. | 
||||| 

Pokud přepnete na zobrazení editoru kódu z návrháře, zde je způsob, jakým **snížení proměnné** akce se zobrazí uvnitř vaší definice aplikace logiky, která je ve formátu JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Nastavit proměnnou

Přiřadit jinou hodnotu pro existující proměnnou, postupujte podle kroků pro [zvýšení proměnné](#increment-value) , které s výjimkou: 

1. Najděte a vyberte **proměnné - nastavená proměnná** akce místo. 

2. Zadejte název proměnné a hodnotu, kterou chcete přiřadit. Nová hodnota a proměnná musí mít stejný datový typ.
Je požadována hodnota, protože tato akce nemá výchozí hodnotu. 

Tady jsou vlastnosti **nastavená proměnná** akce:

| Vlastnost | Požaduje se | Hodnota |  Popis | 
|----------|----------|-------|--------------| 
| Název | Ano | <*Název proměnné*> | Název proměnné změnit | 
| Hodnota | Ano | <*Nová hodnota*> | Hodnota, kterou chcete přiřadit proměnnou. Obě musí mít stejný datový typ. | 
||||| 

> [!NOTE]
> Pokud si nejste zvyšování nebo dekrementace proměnné, změna proměnné uvnitř smyčky *může* vytvořit neočekávané výsledky, protože smyčky jsou spouštěny souběžně nebo souběžně, ve výchozím nastavení. Pro tyto případy zkuste nastavení vaší opakování ve smyčce bude spouští sekvenčně. Například pokud chcete odkazovat na hodnotu proměnné uvnitř smyčky a očekávají, že stejnou hodnotu, při spuštění a ukončení této instance smyčky, použijte následující postup změna způsobu provedení cyklu: 
>
> 1. V pravém horním rohu vaší smyčky, zvolte tlačítko se třemi tečkami (...) a pak zvolte **nastavení**.
> 
> 2. V části **Kontrola souběžnosti**, změnit **přepsat výchozí** nastavení **na**.
>
> 3. Přetáhněte **stupeň paralelismus** posuvníku **1**.

Pokud přepnete na zobrazení editoru kódu z návrháře, zde je způsob, jakým **nastavená proměnná** akce se zobrazí uvnitř vaší definice aplikace logiky, která je ve formátu JSON. Tento příklad změní aktuální hodnota proměnné "Count" s jinou hodnotou. 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Připojit k proměnné

Pro proměnné, které ukládají řetězce nebo pole, můžete vložit nebo *připojit* hodnota proměnné jako poslední položky v těchto řetězce nebo pole. Můžete provést kroky pro [zvýšení proměnné](#increment-value) s tím rozdílem, že místo toho postupujte takto: 

1. Najděte a vyberte jednu z těchto akcí podle toho, jestli vaše proměnná řetězce nebo pole: 

  * **Proměnné - připojit na proměnnou string**
  * **Proměnné – připojení k proměnné pole** 

2. Zadejte hodnotu pro připojení jako poslední položky v řetězec nebo pole. Tato hodnota se vyžaduje. 

Tady jsou vlastnosti **připojit k...**  akce:

| Vlastnost | Požaduje se | Hodnota |  Popis | 
|----------|----------|-------|--------------| 
| Název | Ano | <*Název proměnné*> | Název proměnné změnit | 
| Hodnota | Ano | <*append – hodnota*> | Hodnota, kterou chcete připojit, může mít jakýkoli typ | 
|||||  

Pokud přepnete na zobrazení editoru kódu z návrháře, tady je způsob **připojení k proměnné pole** akce se zobrazí uvnitř vaší definice aplikace logiky, která je ve formátu JSON.
Tento příklad vytvoří proměnné pole a přidá jinou hodnotu jako poslední položky v poli. Vaše výsledkem je aktualizovaný proměnné, která obsahuje toto pole: `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [konektory Logic Apps](../connectors/apis-list.md)
