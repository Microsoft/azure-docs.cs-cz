---
title: Vytváření proměnných pro ukládání hodnot – Azure Logic Apps | Dokumentace Microsoftu
description: Jak ukládat a spravovat hodnoty tak, že vytvoříte proměnné v Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: bb84c7d5e483b0a2abc3b7d1a37de8760513d203
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063212"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Vytvoření proměnné k ukládání a správě hodnoty v Azure Logic Apps

Tento článek popisuje, jak můžete ukládat a pracovat s hodnotami v celé aplikaci logiky tak, že vytvoříte proměnné. Například proměnné vám může pomoct zjistit počet případů, kdy se spustí smyčku. Při iterování přes pole nebo kontrola pole pro konkrétní položky, můžete použít proměnnou tak, aby odkazovaly číslo indexu pro každou položku pole. 

Můžete vytvořit proměnné pro datové typy, například celé číslo, float, logická hodnota, řetězec, pole a objektu. Jakmile vytvoříte proměnnou, můžete provést další úlohy, například:

* Získat nebo odkaz na hodnotu proměnné.
* Zvýšení nebo snížení proměnné podle konstantní hodnoty, označované také jako *přírůstek* a *snížení*.
* Přiřadíte jinou hodnotu do proměnné.
* Vložit nebo *připojit* hodnotu proměnné jako poslední čas v řetězce nebo pole.

Proměnné existují a jsou globální pouze v rámci instanci aplikace logiky, která je vytvořila. Kromě toho jsou zachována i mezi všechny iterace smyčky v instanci aplikace logiky. Při odkazování na proměnné, použijte název proměnné jako token, ne název akce, který je obvykle způsob, jak odkazovat na výstupy akce. 

> [!IMPORTANT]
> Ve výchozím nastavení cykly ve smyčce "Foreach" běžet paralelně. Při použití proměnných ve smyčkách spuštění smyčky [postupně](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) proměnné vrátit předvídatelné výsledky. 

Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>. 

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto článku, tady jsou položky, které potřebujete:

* Aplikace logiky, ve kterém chcete vytvořit proměnnou 

  Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve své aplikaci logiky 

  Před přidáním akce pro vytváření a práci s proměnnými, aplikace logiky musí spouštět triggerem.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializovat proměnnou

Můžete vytvořit proměnnou a deklarovat jeho datový typ a počáteční hodnota – vše v rámci jedné akce ve vaší aplikaci logiky. Je možné deklarovat pouze proměnné na globální úrovni, není v rozsahu obory, podmínek a cyklů. 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s existující aktivační události.

2. Ve vaší aplikaci logiky, v části krok, ve které chcete přidat proměnnou proveďte jeden z těchto kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidat akci](./media/logic-apps-create-variables-store-values/add-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a pak zvolte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "proměnné". Ze seznamu akcí vyberte **proměnné – inicializovat proměnnou**.

   ![Vyberte akci](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Zadejte informace pro vaše proměnná:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | Name | Ano | <*Název proměnné*> | Název proměnné se zvýší | 
   | Type | Ano | <*Typ proměnné*> | Datový typ pro proměnnou | 
   | Value | Ne | <*Počáteční hodnota*> | Počáteční hodnota proměnné <p><p>**Tip**: I když je volitelné, nastavte tuto hodnotu jako osvědčený postup, abyste vždycky věděli, počáteční hodnotu proměnné. | 
   ||||| 

   ![Inicializovat proměnnou](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Teď pokračujte v přidávání akce, které chcete. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

Pokud přejdete z návrháře zobrazení editoru kódu, tady je způsob, jakým **inicializovat proměnnou** akce se zobrazí uvnitř definici aplikace logiky, která je ve formátu JavaScript Object Notation (JSON):

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

Tady jsou příklady pro některé typy proměnných:

*Řetězcová hodnota*

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

*Proměnné typu Boolean*

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

*Pole celých čísel*

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

Načíst nebo odkazovat proměnné obsah, můžete použít také [variables() funkce](../logic-apps/workflow-definition-language-functions-reference.md#variables) v návrháři aplikace logiky a zobrazení editoru kódu.
Při odkazování na proměnné, použijte název proměnné jako token, ne název akce, který je obvykle způsob, jak odkazovat na výstupy akce. 

Například tento výraz získá položky z proměnné pole [vytvořili dříve v tomto článku](#append-value) pomocí **variables()** funkce. **String()** funkce vrátí obsah proměnné ve formátu řetězce: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zvýšení hodnoty proměnné 

Pro zvýšení nebo *přírůstek* proměnnou podle konstantní hodnoty, přidejte **proměnné – zvýšení hodnoty proměnné** akce aplikace logiky. Tato akce funguje pouze s proměnnými celé číslo a plovoucí desetinnou čárkou.

1. V návrháři aplikace logiky, vyberte v části krok, kde chcete navýšit stávající proměnné, **nový krok** > **přidat akci**. 

   Tato aplikace logiky již má například aktivační událost a akce, která vytvoří proměnnou. Ano přidejte novou akci podle těchto kroků:

   ![Přidat akci](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Přidání akce mezi stávající kroky, najeďte myší na šipku připojení tak, aby se zobrazí znaménko plus (+). Vyberte znaménko plus a pak zvolte **přidat akci**.

2. Do vyhledávacího pole zadejte jako filtr "zvýšení hodnoty proměnné". V seznamu akcí vyberte **proměnné – zvýšení hodnoty proměnné**.

   ![Vyberte akci "Zvýšení hodnoty proměnné"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Zadejte informace pro zvýšení vaší proměnné:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | Name | Ano | <*Název proměnné*> | Název proměnné se zvýší | 
   | Value | Ne | <*přírůstková hodnota*> | Hodnota určená pro zvyšování hodnoty proměnné. Výchozí hodnota je 1. <p><p>**Tip**: I když je volitelné, nastavte tuto hodnotu jako osvědčený postup, abyste vždycky věděli, konkrétní hodnota se zvyšuje vaše proměnná. | 
   |||| 

   Příklad: 
   
   ![Příklad hodnoty přírůstku](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

Pokud přejdete z návrháře zobrazení editoru kódu, tady je způsob, jakým **zvýšení hodnoty proměnné** akce se zobrazí uvnitř definici aplikace logiky, která je ve formátu JSON:

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

## <a name="example-create-loop-counter"></a>Příklad: Vytvoření čítače cyklů

Proměnné se obvykle používají pro počet případů, kdy se spustí smyčku počítání. Tento příklad ukazuje, jak vytvořit a používat proměnné pro tuto úlohu tak, že vytvoříte smyčku, která vrátí přílohy v e-mailu.

1. Na webu Azure Portal vytvoření prázdné aplikace logiky. Přidání triggeru, který kontroluje nových e-mailu a přílohy. 

   Tento příklad používá Office 365 Outlook aktivační událost pro **při přijetí nového e-mailu**. 
   Chcete-li vyvolat pouze v případě, že má e-mail přílohy můžete nastavit Tato aktivační událost.
   Však můžete veškerých konektorů, která kontroluje nových e-mailů s přílohami, jako je například konektor Outlook.com.

2. V triggeru, zvolte **zobrazit pokročilé možnosti**. Pokud chcete, aby se aktivační událost kontroly příloh a předat tyto přílohy do pracovních postupů aplikace logiky, vyberte **Ano** pro tyto vlastnosti:
   
   * **Má přílohu** 
   * **Zahrnout přílohy** 

   ![Vyhledat a zahrnout přílohy](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Přidat [ **inicializovat proměnnou** akce](#create-variable). Vytvoření celočíselnou proměnnou s názvem **počet** nulu na začátku počáteční hodnota.

   ![Přidání akce pro "Inicializovat proměnnou"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. K cyklování skrze každou přílohu, přidejte *pro každou* smyčky výběrem **nový krok** > **Další** > **přidat pro každý**.

   ![Přidání smyčky "pro každý"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. Ve smyčce, klikněte do **vybrat výstup z předchozího postupu** pole. Jakmile se zobrazí v seznamu dynamického obsahu, vyberte **přílohy**. 

   ![Výběr možnosti „Přílohy“](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Přílohy** předává pole hodnot, která má e-mailové přílohy z výstupu triggeru do smyčku.

6. Smyčka "for each" vyberte **přidat akci**. 

   ![Vyberte "Přidat akci"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. Do vyhledávacího pole zadejte jako filtr "zvýšení hodnoty proměnné". Ze seznamu akcí vyberte **proměnné – zvýšení hodnoty proměnné**.

   > [!NOTE]
   > Ujistěte se, **zvýšení hodnoty proměnné** akce se zobrazí uvnitř smyčka. Pokud se zobrazí akci mimo smyčku, přetáhněte akce do smyčky.

8. V **zvýšení hodnoty proměnné** akce, z **název** seznamu, vyberte **počet** proměnné. 

   ![Vyberte proměnnou "Počet"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. V rámci smyčky přidejte všechny akce, které vám pošle počet příloh. V akci, zahrnují hodnotu z **počet** proměnných, například: 

   ![Přidání akce, která odesílá výsledky](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

1. Pokud vaše aplikace logiky není povolené, v nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů zvolte **povolit**. 

2. Na panelu nástrojů návrháře aplikace logiky zvolte **spustit**. Tímto krokem ručně spustíte svou aplikaci logiky.

3. Odeslání e-mailu s přílohami přiřaďte jeden nebo více e-mailový účet, který jste použili v tomto příkladu.

   Tento krok spustí trigger aplikace logiky, která vytvoří a spustí instanci pracovního postupu aplikace logiky.
   V důsledku toho aplikace logiky vám odešle zprávu nebo e-mailu, který zobrazuje počet přílohy v e-mailu, který jste odeslali.

Pokud přejdete z návrháře zobrazení editoru kódu, tady je způsob smyčka "for each", zobrazí se **zvýšení hodnoty proměnné** akce v definici aplikace logiky, která je ve formátu JSON.

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

Ke snížení nebo *snížení* proměnnou podle konstantní hodnoty, postupujte podle kroků pro [zvýšení proměnnou](#increment-value) s tím rozdílem, že můžete najít a vybrat **proměnné – Dekrementuje proměnnou**akce místo. Tato akce funguje pouze s proměnnými celé číslo a plovoucí desetinnou čárkou.

Tady jsou vlastnosti **Dekrementuje proměnnou** akce:

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| Name | Ano | <*Název proměnné*> | Název proměnné se sníží | 
| Value | Ne | <*přírůstková hodnota*> | Hodnota dekrementace proměnné. Výchozí hodnota je 1. <p><p>**Tip**: I když je volitelné, nastavte tuto hodnotu jako osvědčený postup, budete vždycky vědět, konkrétní hodnota dekrementace proměnné. | 
||||| 

Pokud přejdete z návrháře zobrazení editoru kódu, tady je způsob, jakým **Dekrementuje proměnnou** akce se zobrazí uvnitř definici aplikace logiky, která je ve formátu JSON.

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

Přiřadit jinou hodnotu existující proměnné, postupujte podle kroků pro [zvýšení proměnnou](#increment-value) , které jste s výjimkou: 

1. Vyhledejte a vyberte **proměnné – nastavená proměnná** akce místo. 

2. Zadejte název proměnné a hodnotu, kterou chcete přiřadit. Nová hodnota a proměnná musí mít stejný datový typ.
Je požadována hodnota, protože tato akce nemá výchozí hodnotu. 

Tady jsou vlastnosti **nastavená proměnná** akce:

| Vlastnost | Požaduje se | Hodnota |  Popis | 
|----------|----------|-------|--------------| 
| Name | Ano | <*Název proměnné*> | Název proměnné, chcete-li změnit | 
| Value | Ano | <*Nová hodnota*> | Hodnota, kterou chcete přiřadit proměnné. Musí mít stejný datový typ. | 
||||| 

> [!NOTE]
> Pokud si nejste zvyšování nebo dekrementace proměnné, změna proměnných uvnitř smyčky *může* vytvořit neočekávaným výsledkům, protože smyčky ve výchozím nastavení spouští paralelně nebo souběžně. Pro tyto případy zkuste nastavit smyčku sekvenční spuštění. Například pokud chcete odkazovat na proměnné hodnotu uvnitř smyčka a očekávají, že na začátku a na konci této instance smyčky stejnou hodnotu, použijte následující postup změna jak cyklu: 
>
> 1. V pravém horním rohu vašeho smyčku, zvolte tlačítko se třemi tečkami (...) a klikněte na tlačítko **nastavení**.
> 
> 2. V části **řízení souběžnosti**, změnit **přepsat výchozí** nastavení **na**.
>
> 3. Přetáhněte **stupeň paralelismu** posuvníku **1**.

Pokud přejdete z návrháře zobrazení editoru kódu, tady je způsob, jakým **nastavená proměnná** akce se zobrazí uvnitř definici aplikace logiky, která je ve formátu JSON. V tomto příkladu se změní aktuální hodnota proměnné "Počet" s jinou hodnotou. 

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

## <a name="append-to-variable"></a>Připojení k proměnné

Pro proměnné, které obsahují řetězce nebo pole, můžete vložit nebo *připojit* hodnota proměnné jako poslední položky v tyto řetězce nebo pole. Můžete provést kroky pro [zvýšení proměnnou](#increment-value) s tím rozdílem, že místo toho postupujte takto: 

1. Vyhledejte a vyberte jednu z těchto akcí podle toho, jestli vaše proměnná řetězce nebo pole: 

  * **Proměnné – připojení k proměnné řetězce**
  * **Proměnné – připojení k proměnné pole** 

2. Zadejte hodnotu pro připojení jako poslední položky v řetězce nebo pole. Tato hodnota se vyžaduje. 

Tady jsou vlastnosti **připojit k...**  akce:

| Vlastnost | Požaduje se | Hodnota |  Popis | 
|----------|----------|-------|--------------| 
| Name | Ano | <*Název proměnné*> | Název proměnné, chcete-li změnit | 
| Value | Ano | <*Přidat hodnotu*> | Hodnota, kterou chcete připojit, který může mít libovolný typ | 
|||||  

Pokud přejdete z návrháře zobrazení editoru kódu, tady je způsob, jakým **připojení k proměnné pole** akce se zobrazí uvnitř definici aplikace logiky, která je ve formátu JSON.
Tento příklad vytvoří proměnné pole a přidá jako poslední položku v poli jinou hodnotu. Sady výsledků je aktualizované proměnnou, která obsahuje tato pole: `[1,2,3,"red"]` 

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
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [konektory Logic Apps](../connectors/apis-list.md)
