---
title: Vytváření a Správa proměnných pro ukládání a předávání hodnot
description: Naučte se ukládat, spravovat, používat a předávat hodnoty pomocí proměnných v automatizovaných úlohách a pracovních postupech, které vytvoříte pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333898"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Ukládání a správa hodnot s využitím proměnných v Azure Logic Apps

Tento článek ukazuje, jak vytvořit a pracovat s proměnnými, které slouží k ukládání hodnot do aplikace logiky. Například proměnné vám pomohou sledovat počet spuštění smyčky. Chcete-li iterovat přes pole nebo zjistit konkrétní položku v poli, můžete použít proměnnou pro odkazování na číslo indexu pro každou položku pole.

Můžete vytvořit proměnné pro datové typy, jako je celé číslo, float, Boolean, String, Array a Object. Po vytvoření proměnné můžete provádět další úlohy, například:

* Získá nebo odkázat na hodnotu proměnné.
* Zvyšte nebo snižte proměnnou pomocí konstantní hodnoty, označované také jako *zvýšení* a *snížení*.
* Přiřaďte proměnné jinou hodnotu.
* Vložte nebo *přidejte* hodnotu proměnné jako poslední položku v řetězci nebo poli.

Proměnné existují a jsou globální jenom v instanci aplikace logiky, která je vytvořila. Také se chovají v rámci všech iterací smyčky v instanci aplikace logiky. Když odkazujete na proměnnou, použijte název proměnné jako token, nikoli název akce, což je obvyklý způsob, jak odkazovat na výstupy akce.

> [!IMPORTANT]
> Ve výchozím nastavení se cyklická smyčka "for each" spouští paralelně. Když použijete proměnné ve smyčce, spusťte smyčku [sekvenčně](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) , aby proměnné vracely předvídatelné výsledky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ve které chcete vytvořit proměnnou

  Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve vaší aplikaci logiky

  Než budete moct přidat akce pro vytváření a práci s proměnnými, musí vaše aplikace logiky začínat triggerem.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializovat proměnnou

Můžete vytvořit proměnnou a deklarovat její datový typ a počáteční hodnotu – vše v rámci jedné akce ve vaší aplikaci logiky. Proměnné můžete deklarovat jenom na globální úrovni, ne v oborech, podmínkách a smyčkách.

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s existující triggerem.

1. V aplikaci logiky v kroku, kam chcete přidat proměnnou, proveďte jeden z následujících kroků: 

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Snímek obrazovky, který zobrazuje akci "nový krok" vybraný na stránce "návrhář aplikace logiky".](./media/logic-apps-create-variables-store-values/add-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. V části **zvolit akci** zadejte do vyhledávacího pole `variables` jako filtr. V seznamu akce vyberte možnost **inicializovat proměnnou**.

   ![Vybrat akci](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Zadejte tyto informace o proměnné, jak je popsáno níže:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | **Název** | Yes | <*název proměnné*> | Název proměnné, která se má zvýšit |
   | **Typ** | Yes | <*typ proměnné*> | Datový typ proměnné |
   | **Hodnota** | No | <*počáteční hodnota*> | Počáteční hodnota proměnné <p><p>**Tip**: Pokud je to volitelné, nastavte tuto hodnotu jako osvědčený postup, abyste vždy znali počáteční hodnotu pro vaši proměnnou. |
   |||||

   Například:

   ![Inicializovat proměnnou](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Nyní pokračujte v přidávání akcí, které chcete. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

Pokud přepnete z návrháře do editoru zobrazení kódu, je zde způsob, jak se akce **inicializovat proměnnou** zobrazí v definici aplikace logiky, která je ve formátu JavaScript Object Notation (JSON):

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
> I když akce **inicializovat proměnnou** má `variables` oddíl, který je strukturován jako pole, akce může najednou vytvořit pouze jednu proměnnou. Každá nová proměnná vyžaduje samostatnou akci **inicializovat proměnnou** .

Tady jsou příklady pro některé jiné typy proměnných:

*Řetězcová proměnná*

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

## <a name="get-the-variables-value"></a>Získat hodnotu proměnné

Pro načtení nebo odkazování na obsah proměnné můžete použít také [funkci Variables ()](../logic-apps/workflow-definition-language-functions-reference.md#variables) v návrháři aplikace logiky a v editoru zobrazení kódu. Při odkazování na proměnnou použijte název proměnné jako token, nikoli název akce, což je obvyklý způsob, jak odkazovat na výstupy akce.

Například tento výraz získá položky z proměnné pole [vytvořené dříve v tomto článku](#append-value) pomocí `variables()` funkce. `string()`Funkce vrátí obsah proměnné ve formátu řetězce:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zvýšit proměnnou 

Pokud chcete proměnnou *zvýšit nebo zvýšit na konstantní* hodnotu, přidejte akci **přírůstku** do vaší aplikace logiky. Tato akce funguje pouze s proměnnými Integer a float.

1. V návrháři aplikace logiky v kroku, kde chcete zvětšit existující proměnnou vyberte **Nový krok**. 

   Například tato aplikace logiky již má Trigger a akci, která vytvořila proměnnou. Proto přidejte novou akci pomocí těchto kroků:

   ![Přidání akce](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus (+). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "přírůstek proměnné". V seznamu akce vyberte možnost **zvýšit proměnnou**.

   ![Vybrat akci zvýšit proměnnou](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Zadejte tyto informace pro zvýšení vaší proměnné:

   | Vlastnost | Požaduje se | Hodnota |  Popis |
   |----------|----------|-------|--------------|
   | **Název** | Yes | <*název proměnné*> | Název proměnné, která se má zvýšit |
   | **Hodnota** | No | <*přírůstek-hodnota*> | Hodnota použitá pro zvýšení proměnné. Výchozí hodnota je jedna. <p><p>**Tip**: Pokud je to volitelné, nastavte tuto hodnotu jako osvědčený postup, abyste vždy znali konkrétní hodnotu pro zvýšení vaší proměnné. |
   ||||

   Například:

   ![Příklad hodnoty přírůstku](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

Pokud přepnete z návrháře do editoru zobrazení kódu, je zde způsob, jakým se akce s **proměnnou přírůstku** zobrazí v definici aplikace logiky, která je ve formátu JSON:

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

## <a name="example-create-loop-counter"></a>Příklad: vytvoření počítadla smyčky

Proměnné se běžně používají pro počítání počtu spuštění smyčky. Tento příklad ukazuje, jak vytvořit a používat proměnné pro tuto úlohu vytvořením smyčky, která bude počítat přílohy v e-mailu.

1. V Azure Portal vytvořte prázdnou aplikaci logiky. Přidejte aktivační událost, která kontroluje nové e-maily a přílohy.

   V tomto příkladu se **při přijetí nového e-mailu** použije trigger Office 365 Outlook. Tuto aktivační událost můžete nastavit tak, aby se aktivovala pouze v případě, že má e-mail přílohy. Můžete ale použít libovolný konektor, který kontroluje nové e-maily s přílohami, jako je konektor Outlook.com.

1. V aktivační události pro kontrolu příloh a předání těchto příloh do pracovního postupu vaší aplikace logiky vyberte **Ano** pro tyto vlastnosti:

   * **Má přílohu**
   * **Zahrnout přílohy**

   ![Kontrolovat a zahrnovat přílohy](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Přidejte akci [ **inicializovat proměnnou**](#create-variable). Vytvořte celočíselnou proměnnou s názvem `Count` , která má nulovou počáteční hodnotu.

   ![Přidat akci pro "inicializovat proměnnou"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Chcete-li procyklovat jednotlivé přílohy, přidejte *pro každou* smyčku.

   1. V akci **inicializovat proměnnou** vyberte **Nový krok**.

   1. V části **zvolit akci** vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `for each` jako filtr hledání a **pro každý** vyberte.

      ![Přidejte smyčku For Each.](./media/logic-apps-create-variables-store-values/add-loop.png)

1. Ve smyčce klikněte do pole **vybrat výstup z předchozího postupu** . Až se zobrazí seznam dynamického obsahu, vyberte **přílohy**.

   ![Výběr možnosti „Přílohy“](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Vlastnost **Attachments** předá do smyčky pole, které obsahuje přílohy e-mailů z výstupu triggeru.

1. V části **pro každou** smyčku vyberte **přidat akci**.

   ![Vyberte přidat akci.](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Do vyhledávacího pole zadejte jako filtr "přírůstek proměnné". V seznamu akce vyberte možnost **zvýšit proměnnou**.

   > [!NOTE]
   > Ujistěte se, že se v rámci smyčky objeví akce **přírůstku** . Pokud se akce objeví mimo smyčku, přetáhněte akci do smyčky.

1. V akci **zvýšit proměnnou** v seznamu **název** vyberte proměnnou **Count** .

   ![Vybrat proměnnou Count](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. V rámci smyčky přidejte jakoukoli akci, která vám pošle počet příloh. V akci zahrňte hodnotu z proměnné **Count** , například:

   ![Přidat akci, která odesílá výsledky](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud vaše aplikace logiky není povolená, vyberte v nabídce aplikace logiky možnost **Přehled**. Na panelu nástrojů vyberte **Povolit**.

1. Na panelu nástrojů návrháře aplikace logiky vyberte **Spustit**. Tento krok ručně spustí vaši aplikaci logiky.

1. Odešlete e-mail s jednou nebo více přílohami k e-mailovému účtu, který jste použili v tomto příkladu.

   Tento krok spustí Trigger aplikace logiky, který vytvoří a spustí instanci pro pracovní postup vaší aplikace logiky. V důsledku toho vám aplikace logiky pošle zprávu nebo e-mail zobrazující počet příloh e-mailů, které jste odeslali.

Pokud přepínáte z návrháře na Editor zobrazení kódu, zde je způsob, jakým **se v rámci** definice aplikace logiky zobrazí akce **pro každou** smyčku a která je ve formátu JSON.

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

## <a name="decrement-variable"></a>Snížit proměnnou

Chcete *-li proměnnou snížit nebo snížit* pomocí konstantní hodnoty, postupujte podle kroků pro [zvýšení proměnné](#increment-value) s výjimkou toho, že vyhledáte a vyberete akci **snížit proměnnou** . Tato akce funguje pouze s proměnnými Integer a float.

Tady jsou vlastnosti pro akci **snížení proměnné** :

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| **Název** | Yes | <*název proměnné*> | Název proměnné, která se má snížit | 
| **Hodnota** | No | <*přírůstek-hodnota*> | Hodnota pro snížení proměnné Výchozí hodnota je jedna. <p><p>**Tip**: Pokud je to volitelné, nastavte tuto hodnotu jako osvědčený postup, abyste vždy znali určitou hodnotu pro snížení vaší proměnné. |
||||| 

Pokud přepnete z návrháře do editoru zobrazení kódu, je zde způsob, jakým se akce **snížení proměnné** zobrazí v definici aplikace logiky, která je ve formátu JSON.

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

Chcete-li přiřadit jinou hodnotu k existující proměnné, postupujte podle kroků pro [zvýšení proměnné](#increment-value) s výjimkou toho, že:

1. Místo toho vyhledejte a vyberte akci **nastavit proměnnou** .

1. Zadejte název proměnné a hodnotu, kterou chcete přiřadit. Nová hodnota i proměnná musí mít stejný datový typ. Hodnota je povinná, protože tato akce nemá výchozí hodnotu.

Tady jsou vlastnosti pro akci **nastavit proměnnou** :

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| **Název** | Yes | <*název proměnné*> | Název proměnné, která se má změnit |
| **Hodnota** | Yes | <*Nová hodnota*> | Hodnota, kterou chcete přiřadit k proměnné. Oba typy musí mít stejný datový typ. |
||||| 

> [!NOTE]
> Pokud nezvyšujete ani nesnižujete proměnné, *může* Změna proměnných uvnitř smyček vznikne neočekávané výsledky, protože smyčky běží paralelně nebo souběžně, ve výchozím nastavení. V těchto případech zkuste nastavit smyčku tak, aby běžela sekvenčně. Například pokud chcete odkazovat na hodnotu proměnné uvnitř smyčky a očekávat stejnou hodnotu na začátku a konci této instance smyčky, postupujte podle těchto kroků a změňte způsob, jakým se spouští smyčka: 
>
> 1. V pravém horním rohu smyčky vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.
> 
> 2. V části **řízení souběžnosti** změňte **výchozí nastavení přepsat** na **zapnuto**.
>
> 3. Přetáhněte jezdec **stupeň paralelismu** na **1**.

Pokud přepnete z návrháře do editoru zobrazení kódu, je zde způsob, jakým se akce **nastavit proměnnou** zobrazí v definici aplikace logiky, která je ve formátu JSON. Tento příklad změní `Count` aktuální hodnotu proměnné na jinou hodnotu.

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

Pro proměnné, které ukládají řetězce nebo pole, můžete vložit nebo *připojit* hodnotu proměnné jako poslední položku v těchto řetězcích nebo polích. Můžete postupovat podle kroků pro [zvýšení proměnné](#increment-value) s výjimkou toho, že jste provedete tyto kroky: 

1. Najděte a vyberte jednu z těchto akcí na základě toho, zda je proměnná řetězec nebo pole: 

   * **Připojit k proměnné řetězce**
   * **Připojit k proměnné pole** 

1. Zadejte hodnotu, která se má připojit jako poslední položka v řetězci nebo poli. Tato hodnota se vyžaduje.

Tady jsou vlastnosti pro akce **připojit k...** :

| Vlastnost | Požaduje se | Hodnota |  Popis |
|----------|----------|-------|--------------|
| **Název** | Yes | <*název proměnné*> | Název proměnné, která se má změnit |
| **Hodnota** | Yes | <*hodnota připojení*> | Hodnota, kterou chcete připojit, což může mít libovolný typ |
|||||

Pokud přepnete z návrháře do editoru zobrazení kódu, je zde způsob, jakým se akce **připojit k proměnné pole** zobrazí v definici aplikace logiky, která je ve formátu JSON. Tento příklad vytvoří proměnnou pole a přidá další hodnotu jako poslední položku v poli. Váš výsledek je aktualizovaná proměnná, která obsahuje toto pole: `[1,2,3,"red"]`

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
