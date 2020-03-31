---
title: Vytváření a správa proměnných pro ukládání a předávání hodnot
description: Zjistěte, jak ukládat, spravovat, používat a předávat hodnoty pomocí proměnných v automatizovaných úlohách a pracovním postupu, které vytvoříte pomocí Aplikací Logiky Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 55984082a6b287e9f7cdca005a24ef3c18032491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456692"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Ukládání a správa hodnot pomocí proměnných v Azure Logic Apps

Tento článek ukazuje, jak vytvořit a pracovat s proměnnými, které slouží k ukládání hodnot v aplikaci logiky. Proměnné vám například mohou pomoci sledovat, kolikrát se smyčka spustí. Chcete-li iterate přes pole nebo zkontrolujte pole pro určitou položku, můžete použít proměnnou odkazovat na číslo indexu pro každou položku pole.

Můžete vytvořit proměnné pro datové typy, jako je celé číslo, float, logická hodnota, řetězec, pole a objekt. Po vytvoření proměnné můžete provádět další úkoly, například:

* Získat nebo odkazovat na hodnotu proměnné.
* Zvětšete nebo zmenšete proměnnou o konstantní hodnotu, označovanou také jako *přírůstek* a *snížení*.
* Přiřaďte proměnné jinou hodnotu.
* Vloží nebo *připojí* hodnotu proměnné jako poslední čas v řetězci nebo poli.

Proměnné existují a jsou globální pouze v rámci instance aplikace logiky, která je vytvoří. Také přetrvávají v celé opakování smyčky uvnitř instance aplikace logiky. Když odkazujete na proměnnou, použijte název proměnné jako token, nikoli název akce, což je obvyklý způsob, jak odkazovat na výstupy akce.

> [!IMPORTANT]
> Ve výchozím nastavení cykly ve smyčce "Pro každý" spustit paralelně. Při použití proměnných ve smyčkách spusťte smyčku [postupně](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) tak, aby proměnné vrátit předvídatelné výsledky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, kde chcete vytvořit proměnnou

  Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [Úvodní příručka: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok v aplikaci logiky

  Před přidáním akcí pro vytváření a práci s proměnnými, aplikace logiky musí začínat aktivační událost.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializovat proměnnou

Můžete vytvořit proměnnou a deklarovat její datový typ a počáteční hodnotu – to vše v rámci jedné akce v aplikaci logiky. Můžete deklarovat pouze proměnné na globální úrovni, nikoli v rámci oborů, podmínek a smyček.

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s existující aktivační událostí.

1. V aplikaci logiky v kroku, kam chcete přidat proměnnou, postupujte jedním z těchto kroků: 

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Přidání akce](./media/logic-apps-create-variables-store-values/add-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na**+** spojovací šipku tak, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. V části **Zvolte akci**zadejte `variables` do vyhledávacího pole jako filtr. V seznamu akcí vyberte **Možnost Inicializovat proměnnou**.

   ![Vybrat akci](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Zadejte tyto informace o proměnné, jak je popsáno níže:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | **Název** | Ano | <*název proměnné*> | Název proměnné na přírůstek |
   | **Typ** | Ano | <*typ proměnné*> | Datový typ pro proměnnou |
   | **Hodnotu** | Ne | <*počáteční hodnota*> | Počáteční hodnota proměnné <p><p>**Tip:** I když je tato hodnota volitelná, nastavte tuto hodnotu jako osvědčený postup, abyste vždy znali počáteční hodnotu proměnné. |
   |||||

   Například:

   ![Inicializovat proměnnou](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Nyní pokračujte v přidávání požadovaných akcí. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

Pokud přepnete z návrháře do editoru zobrazení kódu, tady je způsob, jakým se akce **proměnné Initialize** zobrazí v definici aplikace logiky, která je ve formátu JavaScript Object Notation (JSON):

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

> [!NOTE]
> Přestože akce **Proměnná Inicializovat** má `variables` oddíl, který je strukturován jako pole, akce může vytvořit pouze jednu proměnnou najednou. Každá nová proměnná vyžaduje individuální akci **proměnné Initialize.**

Zde jsou příklady pro některé další typy proměnných:

*Proměnná řetězce*

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

*Logická proměnná*

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

*Pole s celámi*

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

## <a name="get-the-variables-value"></a>Získání hodnoty proměnné

Chcete-li načíst nebo odkazovat na obsah proměnné, můžete také použít [funkci variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) v Návrháři aplikace logiky a editoru zobrazení kódu. Při odkazování na proměnnou použijte název proměnné jako token, nikoli název akce, což je obvyklý způsob, jak odkazovat na výstupy akce.

Například tento výraz získá položky z proměnné pole vytvořené `variables()` dříve v tomto [článku](#append-value) pomocí funkce. Funkce `string()` vrátí obsah proměnné ve formátu řetězce:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Proměnná přírůstek 

Chcete-li zvýšit nebo *zvýšit* proměnnou konstantní hodnotou, přidejte akci **proměnné Přírůstek** do aplikace logiky. Tato akce funguje pouze s celými a plovoucími proměnnými.

1. V Návrháři aplikace logiky vyberte v kroku, kde chcete zvýšit existující proměnnou, **nový krok**. 

   Například tato aplikace logiky již má aktivační událost a akci, která vytvořila proměnnou. Proto přidejte novou akci v těchto krocích:

   ![Přidání akce](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši nad spojovací šipku tak, aby se znaménko plus (+) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "proměnná přírůstku". V seznamu akcí vyberte **proměnná Přírůstek**.

   ![Vyberte akci "Proměnná přírůstku"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Zadejte tyto informace pro zvýšení proměnné:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | **Název** | Ano | <*název proměnné*> | Název proměnné na přírůstek |
   | **Hodnotu** | Ne | <*přírůstek-hodnota*> | Hodnota použitá pro zvýšení proměnné. Výchozí hodnota je jedna. <p><p>**Tip:** I když je tato hodnota volitelná, nastavte tuto hodnotu jako osvědčený postup, abyste vždy znali konkrétní hodnotu pro zvýšení proměnné. |
   ||||

   Například:

   ![Příklad hodnoty přírůstku](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

Pokud přepnete z návrháře do editoru zobrazení kódu, tady je způsob, jakým se akce **proměnné Přírůstek** zobrazí uvnitř definice aplikace logiky, která je ve formátu JSON:

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

## <a name="example-create-loop-counter"></a>Příklad: Vytvořit čítač smyčky

Proměnné se běžně používají pro počítání počtu spuštění smyčky. Tento příklad ukazuje, jak vytvořit a používat proměnné pro tento úkol vytvořením smyčky, která počítá přílohy v e-mailu.

1. Na webu Azure Portal vytvořte prázdnou aplikaci logiky. Přidejte aktivační událost, která kontroluje nové e-maily a přílohy.

   Tento příklad používá aktivační událost Outlooku Office 365 pro **when a new email arrives**. Tuto aktivační událost můžete nastavit tak, aby se spustila pouze v případě, že e-mail obsahuje přílohy. Můžete však použít libovolný konektor, který kontroluje nové e-maily s přílohami, jako je například konektor Outlook.com.

1. Chcete-li v aktivační události vyhledat přílohy a předat je do pracovního postupu aplikace logiky, vyberte **ano** pro tyto vlastnosti:

   * **Má přílohu**
   * **Zahrnout přílohy**

   ![Kontrola a zahrnutí příloh](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Přidejte akci [ **proměnné Inicializovat** ](#create-variable). Vytvořte integerovou `Count` proměnnou s názvem s nulovou počáteční hodnotou.

   ![Přidat akci pro "Inicializovat proměnnou"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Chcete-li procházet každou přílohu, přidejte *pro každou* smyčku.

   1. V části **Akce Inicializovat proměnnou** vyberte **Nový krok**.

   1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `for each` zadejte jako vyhledávací filtr a vyberte **Pro každý**.

      ![Přidání smyčky "pro každou"](./media/logic-apps-create-variables-store-values/add-loop.png)

1. Ve smyčce klikněte do pole **Vybrat výstup z předchozích kroků.** Po zobrazení seznamu dynamického obsahu vyberte **Přílohy**.

   ![Výběr možnosti „Přílohy“](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Vlastnost **Attachments** předá pole, které má přílohy e-mailu z výstupu aktivační události do smyčky.

1. Ve smyčce **Pro každou** z nich vyberte **Přidat akci**.

   ![Vyberte "Přidat akci"](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Do vyhledávacího pole zadejte jako filtr "proměnná přírůstku". V seznamu akcí vyberte **proměnná Přírůstek**.

   > [!NOTE]
   > Ujistěte se, že se uvnitř smyčky zobrazí **proměnná přírůstek.** Pokud se akce zobrazí mimo smyčku, přetáhněte akci do smyčky.

1. V akci **Proměnná přírůstek** vyberte ze seznamu **Název** proměnnou **Count.**

   ![Vybrat proměnnou "Počítat"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Pod smyčku přidejte libovolnou akci, která vám pošle počet příloh. Do akce zahrňte hodnotu z proměnné **Count,** například:

   ![Přidání akce, která odesílá výsledky](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud vaše aplikace logiky není povolená, v nabídce aplikace logiky vyberte **Přehled**. Na panelu nástrojů vyberte **Povolit**.

1. Na panelu nástrojů Návrhář epoje vyberte **Spustit**. Tento krok ručně spustí aplikaci logiky.

1. Pošlete e-mail s jednou nebo více přílohami na e-mailový účet, který jste použili v tomto příkladu.

   Tento krok spustí aktivační událost aplikace logiky, která vytvoří a spustí instanci pracovního postupu vaší aplikace logiky. V důsledku toho aplikace logiky odešle zprávu nebo e-mail, který zobrazuje počet příloh v e-mailu, který jste odeslali.

Pokud přepnete z návrháře do editoru zobrazení kódu, zde je způsob, jakým **pro každou** smyčku se zobrazí spolu s akce **proměnné přírůstek** uvnitř definice aplikace logiky, která je ve formátu JSON.

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

## <a name="decrement-variable"></a>Proměnná snížení

Chcete-li proměnnou snížit nebo *snížit* o konstantní hodnotu, postupujte podle pokynů pro zvýšení proměnné s tím [rozdílem,](#increment-value) že najdete a místo toho vyberte **akci proměnné Snížení.** Tato akce funguje pouze s celými a plovoucími proměnnými.

Tady jsou vlastnosti **akce proměnné snížení:**

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| **Název** | Ano | <*název proměnné*> | Název proměnné ke snížení | 
| **Hodnotu** | Ne | <*přírůstek-hodnota*> | Hodnota pro snížení proměnné. Výchozí hodnota je jedna. <p><p>**Tip:** I když je tato hodnota volitelná, nastavte tuto hodnotu jako osvědčený postup, abyste vždy znali konkrétní hodnotu pro snížení proměnné. |
||||| 

Pokud přepnete z návrháře do editoru zobrazení kódu, tady je způsob, jakým se akce **proměnné Decrement** zobrazí uvnitř definice aplikace logiky, která je ve formátu JSON.

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

Chcete-li existující proměnné přiřadit jinou hodnotu, postupujte podle pokynů pro [zvýšení proměnné](#increment-value) s tím rozdílem, že:

1. Místo toho vyhledejte a vyberte akci **Nastavit proměnnou.**

1. Zadejte název proměnné a hodnotu, kterou chcete přiřadit. Nová hodnota i proměnná musí mít stejný datový typ. Hodnota je vyžadována, protože tato akce nemá výchozí hodnotu.

Zde jsou vlastnosti akce **Nastavit proměnnou:**

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| **Název** | Ano | <*název proměnné*> | Název proměnné, která se má změnit |
| **Hodnotu** | Ano | <*nová hodnota*> | Hodnota, kterou chcete přiřadit proměnnou. Oba musí mít stejný datový typ. |
||||| 

> [!NOTE]
> Pokud nesumítáte nebo nesnižujete proměnné, změna proměnných uvnitř smyček *může* způsobit neočekávané výsledky, protože smyčky běží ve výchozím nastavení paralelně nebo souběžně. V těchto případech zkuste nastavit smyčku spustit postupně. Například pokud chcete odkazovat na hodnotu proměnné uvnitř smyčky a očekávat stejnou hodnotu na začátku a na konci této instance smyčky, postupujte takto změnit způsob spuštění smyčky: 
>
> 1. V pravém horním rohu smyčky vyberte tlačítko tři tečky (**...**) a pak vyberte **Nastavení**.
> 
> 2. V části **Řízení souběžnosti**změňte výchozí nastavení **přepsání** **na Zapnuto**.
>
> 3. Přetáhněte jezdec **Stupeň paralelismu** na **1**.

Pokud přepnete z návrháře do editoru zobrazení kódu, tady je způsob, jakým **nastavit akci proměnné** se zobrazí uvnitř definice aplikace logiky, která je ve formátu JSON. Tento příklad `Count` změní aktuální hodnotu proměnné na jinou hodnotu.

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

Pro proměnné, které ukládají řetězce nebo pole, můžete vložit nebo *připojit* hodnotu proměnné jako poslední položku v těchto řetězcích nebo polích. Můžete postupovat podle kroků pro [zvýšení proměnné](#increment-value) s tím rozdílem, že budete postupovat podle následujících kroků místo: 

1. Vyhledání a vyhledání jedné z těchto akcí na základě toho, zda je proměnná řetězec nebo pole: 

   * **Připojit k řetězci proměnné**
   * **Připojit k proměnné pole** 

1. Zadejte hodnotu připojit jako poslední položku v řetězci nebo matici. Tato hodnota se vyžaduje.

Zde jsou vlastnosti pro **připojit k...** akce:

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| **Název** | Ano | <*název proměnné*> | Název proměnné, která se má změnit |
| **Hodnotu** | Ano | <*hodnota připojení*> | Hodnota, kterou chcete připojit, která může mít libovolný typ |
|||||

Pokud přepnete z návrháře do editoru zobrazení kódu, tady je způsob, jakým **připojit do pole proměnné** akce se zobrazí uvnitř definice aplikace logiky, která je ve formátu JSON. Tento příklad vytvoří proměnnou pole a přidá další hodnotu jako poslední položku v poli. Výsledkem je aktualizovaná proměnná, která obsahuje toto pole:`[1,2,3,"red"]`

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

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech Logic Apps](../connectors/apis-list.md)
