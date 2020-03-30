---
title: Provádění operací s daty
description: Převod, správa a manipulace s datovými výstupy a formáty v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283937"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Provádění datových operací v Aplikacích Azure Logic Apps

Tento článek ukazuje, jak můžete pracovat s daty v aplikacích logiky přidáním akcí pro tyto úkoly a další:

* Vytvořte tabulky z polí.
* Vytvořte pole z jiných polí na základě podmínky.
* Vytvořte uživatelsky přívětivé tokeny z vlastností objektu JavaScript Object Notation (JSON), abyste tyto vlastnosti mohli snadno použít ve svém pracovním postupu.

Pokud zde nenajdete požadovanou akci, zkuste procházet mnoho různých [funkcí pro manipulaci s daty,](../logic-apps/workflow-definition-language-functions-reference.md) které azure logic apps poskytuje.

Tyto tabulky shrnují operace dat, které můžete použít, a jsou uspořádány na základě typů zdrojových dat, na kterých operace pracují, ale každý popis se zobrazí abecedně.

**Akce pole** 

Tyto akce vám pomohou pracovat s daty v polích.

| Akce | Popis |
|--------|-------------|
| [**Vytvořit tabulku CSV**](#create-csv-table-action) | Vytvořte tabulku hodnoty oddělené čárkami (CSV) z pole. |
| [**Vytvořit tabulku HTML**](#create-html-table-action) | Vytvořte tabulku HTML z pole. |
| [**Pole filtru**](#filter-array-action) | Vytvořte podmnožinu pole z pole na základě zadaného filtru nebo podmínky. |
| [**Připojit**](#join-action) | Vytvořte řetězec ze všech položek v poli a oddělte každou položku se zadaným znakem. |
| [**Vyberte**](#select-action) | Vytvořte pole ze zadaných vlastností pro všechny položky v jiném poli. |
||| 

**Akce JSON**

Tyto akce vám pomohou pracovat s daty ve formátu JSON (JavaScript Object Notation).

| Akce | Popis |
|--------|-------------|
| [**Vytvořit**](#compose-action) | Vytvořte zprávu nebo řetězec z více vstupů, které mohou mít různé datové typy. Tento řetězec pak můžete použít jako jeden vstup, nikoli opakovaně zadávat stejné vstupy. Můžete například vytvořit jednu zprávu JSON z různých vstupů. |
| [**Analyzovat JSON**](#parse-json-action) | Vytvořte uživatelsky přívětivé datové tokeny pro vlastnosti v obsahu JSON, abyste mohli snadněji používat vlastnosti ve vašich aplikacích logiky. |
|||

Chcete-li vytvořit složitější transformace JSON, přečtěte si informace o [provádění pokročilých transformací JSON pomocí tekutých šablon](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, kde potřebujete operaci pro práci s daty

  Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [Úvodní příručka: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok v aplikaci logiky 

  Operace s daty jsou k dispozici pouze jako akce, takže před použitím těchto akcí spusťte aplikaci logiky s aktivační událostí a zahrňte všechny další akce potřebné k vytvoření požadovaných výstupů.

<a name="compose-action"></a>

## <a name="compose-action"></a>Akce pro skládání

Chcete-li vytvořit jeden výstup, jako je například objekt JSON z více vstupů, můžete použít akci **Compose.** Vaše vstupy mohou mít různé typy, jako jsou celá čísla, logické hodnoty, pole, objekty JSON a jakýkoli jiný nativní typ, který Azure Logic Apps podporuje, například binární a XML. Výstup pak můžete použít v akcích, které následují po akci **Compose.** Akce **Compose** můžete také ušetřit opakované zadávání stejných vstupů při vytváření pracovního postupu aplikace logiky.

Můžete například vytvořit zprávu JSON z více proměnných, jako jsou proměnné řetězce, které ukládají křestní jména a příjmení lidí a celou proměnnou, která ukládá věk lidí. Akce **Compose** zde přijímá tyto vstupy:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

a vytvoří tento výstup:

`{"age":35,"fullName":"Owens,Sophie"}`

Chcete-li vyzkoušet příklad, postupujte podle následujících kroků pomocí Návrháře aplikací logiky. Nebo pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad **Skládání** a **inicializovat** definice proměnných z tohoto článku do základní definice pracovního postupu vlastní aplikace logiky: [Příklady kódu operace data - Compose](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a několika **akcemi proměnných Initialize.** Tyto akce jsou nastaveny pro vytvoření dvou proměnných řetězce a celé proměnné. Když později otestujete aplikaci logiky, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   ![Spuštění ukázkové aplikace logiky pro akci "Sestavit"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. V aplikaci logiky, kde chcete vytvořit výstup, postupujte jedním z následujících kroků: 

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Vyberte "Nový krok" pro akci "Sestavit"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. V části **Zvolte akci**zadejte `compose` do vyhledávacího pole jako filtr. Ze seznamu akcí vyberte akci **Compose.**

   ![Vybrat akci "Sestavit"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. V poli **Vstupy** zadejte požadované vstupy pro vytvoření výstupu.

   V tomto příkladu se po klepnutí do pole **Vstupy** zobrazí seznam dynamického obsahu, abyste mohli vybrat dříve vytvořené proměnné:

   ![Vyberte vstupy, které chcete použít pro akci "Sestavit".](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Zde je hotový příklad **compose** akce: 

   ![Dokončený příklad akce "Sestavit"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Compose action](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li ověřit, zda akce **Compose** vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce **Compose.**

1. V aplikaci logiky přidejte akci, která vám může poslat výsledky z akce **Compose.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Po otevření seznamu dynamického obsahu vyberte v části **Akce Sestavit** **položku Výstup**.

   Tento příklad používá akci **Odeslat e-mail** a obsahuje pole **Výstup** v textu a předmětu e-mailu:

   ![Pole "Výstup" pro akci "Sestavit"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky akce "Compose"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Vytvořit akci tabulky CSV

Chcete-li vytvořit tabulku hodnot oddělených čárkami (CSV), která má vlastnosti a hodnoty z objektů Zápisu objektu JavaScriptu (JSON) v poli, použijte akci **Vytvořit tabulku CSV.** Výslednou tabulku pak můžete použít v akcích, které následují po akci **Vytvořit tabulku CSV.**

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad **Vytvořit tabulku CSV** a **Inicializovat** definice proměnných z tohoto článku do základní definice pracovního postupu vlastní aplikace [logiky: Příklady kódu operace data - Vytvoření tabulky CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a akce **proměnné Initialize.** Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které má některé vlastnosti a hodnoty ve formátu JSON. Když později otestujete aplikaci logiky, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   ![Spuštění ukázkové aplikace logiky pro akci Vytvořit tabulku CSV](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. V aplikaci logiky, kde chcete vytvořit tabulku CSV, postupujte jedním z následujících kroků: 

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Vyberte akci "Nový krok" pro akci Vytvořit tabulku CSV.](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. V části **Zvolte akci**zadejte `create csv table` do vyhledávacího pole jako filtr. Ze seznamu akcí vyberte akci **Vytvořit tabulku CSV.**

   ![Vyberte akci Vytvořit tabulku CSV.](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. V poli **Od** zadejte pole nebo výraz, který chcete vytvořit tabulku.

   V tomto příkladu se po klepnutí do pole **Od** zobrazí seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Výběr výstupu pole pro vytvoření tabulky CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Chcete-li vytvořit uživatelsky přívětivé tokeny pro vlastnosti v objektech JSON, abyste mohli tyto vlastnosti vybrat jako vstupy, použijte před voláním akce **vytvořit tabulku CSV** položku [Parse JSON.](#parse-json-action)

   Zde je hotový příklad Vytvořit akci **tabulky CSV:** 

   ![Dokončený příklad akce Vytvoření tabulky CSV](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="customize-table-format"></a>Přizpůsobení formátu tabulky

Ve výchozím nastavení je vlastnost **Sloupce** nastavena tak, aby automaticky vytvářela sloupce tabulky na základě položek pole. Chcete-li zadat vlastní záhlaví a hodnoty, postupujte takto:

1. Otevřete seznam **Sloupce** a vyberte **Vlastní**.

1. Ve vlastnosti **Header** zadejte vlastní text záhlaví, který se má použít.

1. Ve vlastnosti **Value** zadejte vlastní hodnotu, která se má použít.

Chcete-li vrátit hodnoty z pole, můžete použít [ `item()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#item) s akcí **vytvořit tabulku CSV.** Ve `For_each` smyčce můžete použít [ `items()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#items).

Předpokládejme například, že chcete sloupce tabulky, které mají pouze hodnoty vlastností a nikoli názvy vlastností z pole. Chcete-li vrátit pouze tyto hodnoty, postupujte podle následujících kroků pro práci v zobrazení návrháře nebo v zobrazení kódu. Zde je výsledek, který tento příklad vrátí:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Práce v návrhářském zobrazení

V akci ponechte sloupec **Záhlaví** prázdný. Na každém řádku ve sloupci **Hodnota** můžete odkazovat na každou požadovanou vlastnost pole. Každý řádek v části **Hodnota** vrátí všechny hodnoty pro zadanou vlastnost pole a stane se sloupcem v tabulce.

1. V části **Hodnota**klikněte na každý požadovaný řádek do textového pole, aby se zobrazil seznam dynamického obsahu.

1. V seznamu dynamického obsahu vyberte **Výraz**.

1. V editoru výrazů zadejte tento výraz, který určuje požadovanou hodnotu vlastnosti pole, a vyberte **OK**.

   `item()?['<array-property-name>']`

   Například:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereference "Popis" pro "Vytvořit tabulku CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Opakujte předchozí kroky pro každou požadovanou vlastnost pole. Po dokončení akce vypadá jako tento příklad:

   !["item()" v "Vytvořit tabulku CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Chcete-li vyřešit výrazy do popisnějších verzí, přepněte do zobrazení kódu a zpět do zobrazení návrháře a znovu otevřete sbalenou akci:

   Akce **vytvořit tabulku CSV** se nyní zobrazuje jako tento příklad:

   !["Vytvořit tabulku CSV" - vyřešené výrazy, bez záhlaví](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Práce v zobrazení kódu

V definici JSON akce v `columns` rámci pole `header` nastavte vlastnost na prázdný řetězec. Pro `value` každou vlastnost, odkazovat na každou vlastnost pole, které chcete.

1. Na panelu nástrojů návrháře vyberte **zobrazení kód .**

1. V editoru kódu přidejte `columns` do pole akce `header` prázdnou `value` vlastnost a tento výraz pro každý sloupec požadovaných hodnot pole:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Například:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Přepněte zpět do zobrazení návrháře a znovu otevřete sbalenou akci.

   Akce **vytvořit tabulku CSV** se nyní zobrazí jako tento příklad a výrazy se rozhodly na popisnější verze:

   !["Vytvořit tabulku CSV" - vyřešené výrazy a žádná záhlaví](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Akce Tabulka](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li **ověřit,** zda akce vytvořit tabulku CSV vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce **Vytvořit tabulku CSV.**

1. V aplikaci logiky přidejte akci, která vám může poslat výsledky z akce **Vytvořit tabulku CSV.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Po otevření seznamu dynamického obsahu vyberte v části **Vytvořit akci tabulky CSV** **položku Výstup**. 

   Tento příklad používá akci **Odeslat e-mail** v Outlooku Office 365 a do těla e-mailu obsahuje pole **Výstup:**

   ![Pole "Výstup" pro akci Vytvořit tabulku CSV](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky akce Vytvořit tabulku CSV](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Vytvořit akci tabulky HTML

Chcete-li vytvořit tabulku HTML, která má vlastnosti a hodnoty z objektů Zápisu objektu JavaScriptu (JSON) v poli, použijte akci **Vytvořit tabulku HTML.** Výslednou tabulku pak můžete použít v akcích, které následují po akci **Vytvořit tabulku HTML.**

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad **Vytvořit tabulku HTML** a **Inicializovat** definice proměnných z tohoto článku do základní definice pracovního postupu vlastní aplikace [logiky: Příklady kódu operace data - Vytvořit tabulku HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a akce **proměnné Initialize.** Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které má některé vlastnosti a hodnoty ve formátu JSON. Když později otestujete aplikaci logiky, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   ![Spuštění ukázkové aplikace logiky pro "Vytvořit tabulku HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. V aplikaci logiky, kde chcete vytvořit tabulku HTML, postupujte jedním z následujících kroků:

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Vyberte akci "Nový krok" pro akci Vytvořit tabulku HTML.](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. V části **Zvolte akci**zadejte `create html table` do vyhledávacího pole jako filtr. Ze seznamu akcí vyberte akci **Vytvořit tabulku HTML.**

   ![Vyberte akci "Vytvořit tabulku HTML".](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. V poli **Od** zadejte pole nebo výraz, který chcete vytvořit tabulku.

   V tomto příkladu se po klepnutí do pole **Od** zobrazí seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Výběr výstupu pole pro vytvoření tabulky HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Chcete-li vytvořit uživatelsky přívětivé tokeny pro vlastnosti v objektech JSON, abyste mohli tyto vlastnosti vybrat jako vstupy, použijte před voláním akce **Vytvořit tabulku HTML** položku [Parse JSON.](#parse-json-action)

   Zde je hotový příklad Vytvořit akci **tabulky HTML:**

   ![Hotový příklad pro "Vytvořit tabulku HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="customize-table-format"></a>Přizpůsobení formátu tabulky

Ve výchozím nastavení je vlastnost **Sloupce** nastavena tak, aby automaticky vytvářela sloupce tabulky na základě položek pole. Chcete-li zadat vlastní záhlaví a hodnoty, postupujte takto:

1. Otevřete seznam **Sloupce** a vyberte **Vlastní**.

1. Ve vlastnosti **Header** zadejte vlastní text záhlaví, který se má použít.

1. Ve vlastnosti **Value** zadejte vlastní hodnotu, která se má použít.

Chcete-li vrátit hodnoty z pole, můžete tuto [ `item()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#item) použít s akcí **Vytvořit tabulku HTML.** Ve `For_each` smyčce můžete použít [ `items()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#items).

Předpokládejme například, že chcete sloupce tabulky, které mají pouze hodnoty vlastností a nikoli názvy vlastností z pole. Chcete-li vrátit pouze tyto hodnoty, postupujte podle následujících kroků pro práci v zobrazení návrháře nebo v zobrazení kódu. Zde je výsledek, který tento příklad vrátí:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Práce v návrhářském zobrazení

V akci ponechte sloupec **Záhlaví** prázdný. Na každém řádku ve sloupci **Hodnota** můžete odkazovat na každou požadovanou vlastnost pole. Každý řádek v části **Hodnota** vrátí všechny hodnoty pro zadanou vlastnost a stane se sloupcem v tabulce.

1. V části **Hodnota**klikněte na každý požadovaný řádek do textového pole, aby se zobrazil seznam dynamického obsahu.

1. V seznamu dynamického obsahu vyberte **Výraz**.

1. V editoru výrazů zadejte tento výraz, který určuje požadovanou hodnotu vlastnosti pole, a vyberte **OK**.

   `item()?['<array-property-name>']`

   Například:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereference, vlastnost v akci Vytvořit tabulku HTML](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Opakujte předchozí kroky pro každou požadovanou vlastnost pole. Po dokončení akce vypadá jako tento příklad:

   ![funkce "item()" v "Vytvořit tabulku HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Chcete-li vyřešit výrazy do popisnějších verzí, přepněte do zobrazení kódu a zpět do zobrazení návrháře a znovu otevřete sbalenou akci:

   Akce **Vytvořit tabulku HTML** se nyní zobrazuje jako tento příklad:

   !["Vytvořit tabulku HTML" - vyřešené výrazy, bez záhlaví](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Práce v zobrazení kódu

V definici JSON akce v `columns` rámci pole `header` nastavte vlastnost na prázdný řetězec. Pro `value` každou vlastnost, odkazovat na každou vlastnost pole, které chcete.

1. Na panelu nástrojů návrháře vyberte **zobrazení kód .**

1. V editoru kódu přidejte `columns` do pole akce `header` prázdnou `value` vlastnost a tento výraz pro každý sloupec požadovaných hodnot pole:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Například:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Přepněte zpět do zobrazení návrháře a znovu otevřete sbalenou akci.

   Akce **Vytvořit tabulku HTML** se nyní jeví jako v tomto příkladu a výrazy se rozhodly na popisnější verze:

   !["Vytvořit tabulku HTML" - vyřešené výrazy a žádná záhlaví](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Akce Tabulka](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li ověřit, zda akce **Vytvořit tabulku HTML** vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce Vytvořit tabulku **HTML.**

1. V aplikaci logiky přidejte akci, která vám může poslat výsledky z akce **Vytvořit tabulku HTML.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Po otevření seznamu dynamického obsahu vyberte v části **Akce Vytvořit tabulku HTML** **položku Výstup**. 

   Tento příklad používá akci **Odeslat e-mail** v Outlooku Office 365 a do těla e-mailu obsahuje pole **Výstup:**

   ![Pole "Výstup" pro "Vytvořit tabulku HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Při zahrnutí výstupu tabulky HTML do akce e-mailu se ujistěte, že jste v rozšířených možnostech akce e-mailu nastavili vlastnost **Is HTML** na **ano.** Tímto způsobem akce e-mailu správně formátuje tabulku HTML.

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky "Vytvořit tabulku HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Akce pole filtru

Chcete-li vytvořit menší pole, které má položky, které splňují určitá kritéria, z existujícího pole, použijte akci **pole Filtr.** Filtrované pole pak můžete použít v akcích, které následují po akci **pole Filtr.**

> [!NOTE]
> Jakýkoli text filtru, který použijete ve stavu, rozlišuje malá a velká písmena. Tato akce také nelze změnit formát nebo součásti položek v poli. 
> 
> Pro akce, které mají použít výstup pole z akce **pole Filtr,** musí tyto akce přijmout pole jako vstup nebo je možné transformovat výstupní pole do jiného kompatibilního formátu.
> 
> Pokud zavoláte koncový bod HTTP a obdržíte odpověď JSON, použijte akci **Parse JSON** ke zpracování odpovědi JSON. 
> V opačném případě **akce pole filtrovat** můžete číst pouze tělo odpovědi a nikoli strukturu datové části JSON.

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat ukázkové **pole Filter** a **inicializovat** definice akcí proměnných z tohoto článku do základní definice pracovního postupu vlastní aplikace logiky: [Příklady kódu operace data - Pole filtrů](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a akce **proměnné Initialize.** Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které má některé ukázkové celá čísla. Když později otestujete aplikaci logiky, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   > [!NOTE]
   > Přestože tento příklad používá jednoduché celé pole, tato akce je užitečná zejména pro pole objektů JSON, kde můžete filtrovat na základě vlastností a hodnot objektů.

   ![Spuštění ukázkové aplikace logiky pro akci "Pole filtru"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. V aplikaci logiky, kde chcete vytvořit filtrované pole, postupujte jedním z následujících kroků: 

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Vyberte akci "Nový krok" pro akci "Pole filtru"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole `filter array` zadejte jako filtr. Ze seznamu akcí vyberte akci **pole Filtr.**

   ![Vybrat akci Pole filtru](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. V poli **Od** zadejte pole nebo výraz, který chcete filtrovat.

   V tomto příkladu se po klepnutí do pole **Od** zobrazí seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Výběr výstupu pole pro vytvoření filtrovaného pole](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Pro podmínku zadejte položky pole k porovnání, vyberte operátor porovnání a zadejte hodnotu porovnání.

   Tento příklad `item()` používá funkci pro přístup ke každé položce v poli, zatímco akce **pole Filtr** hledá položky pole, jejichž hodnota je větší než jedna:

   ![Dokončený příklad akce "Pole filtru"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Akce dotazu](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li ověřit, zda akce **pole filtr** vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce **pole Filtr.**

1. V aplikaci logiky přidejte akci, která vám může odeslat výsledky z akce **pole Filtr.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Po otevření seznamu dynamického obsahu vyberte **výraz**. Chcete-li získat výstup pole z akce **pole Filtr,** zadejte tento výraz, který obsahuje název akce **pole Filtr:**

   `@actionBody('Filter_array')`

   Tento příklad používá akci **Odeslat e-mail** v Outlooku Office 365 a zahrnuje výstupy z výrazu **actionBody('Filter_array'** v textu e-mailu:

   ![Výstupy akce z akce "Pole filtru"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky akce "Pole filtru"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Připojit se k akci

Chcete-li vytvořit řetězec, který má všechny položky z pole a oddělit tyto položky s určitým znakem oddělovače, použijte akci **Spojit.** Řetězec pak můžete použít v akcích, které následují po akci **Připojit.**

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad **Join** a **Initialize definice akce proměnné** z tohoto článku do základní definice pracovního postupu vlastní aplikace [logiky: Příklady kódu operace data - Připojit](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a akce **proměnné Initialize.** Tato akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které má některé ukázkové celá čísla. Když otestujete aplikaci logiky později, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   ![Spuštění ukázkové aplikace logiky pro akci Připojit se](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. V aplikaci logiky, kde chcete vytvořit řetězec z pole, postupujte jedním z následujících kroků:

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![SSelect "Nový krok" pro akci "Připojit se"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole `join` zadejte jako filtr. Ze seznamu akcí vyberte tuto akci: **Připojit se**

   ![Vybrat akci Připojit se](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. V poli **Od** zadejte pole, které má položky, které chcete připojit jako řetězec.

   V tomto příkladu, když klepnete do pole **Od,** zobrazí se seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:  

   ![Výběr výstupu pole pro vytvoření řetězce](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Do pole **Spojit s** zadejte požadovaný znak pro oddělení jednotlivých položek pole. 

   Tento příklad používá dvojtečku (:) jako oddělovač.

   ![Zadejte znak oddělovače](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Akce Připojit se](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li **ověřit,** zda akce Připojit vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce **Připojit.**

1. V aplikaci logiky přidejte akci, která vám může poslat výsledky akce **Připojit se.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Po otevření seznamu dynamického obsahu vyberte v části **Připojit** položku **Výstup**. 

   Tento příklad používá akci **Odeslat e-mail** v Outlooku Office 365 a do těla e-mailu obsahuje pole **Výstup:**

   ![Pole "Výstup" pro akci Připojit](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky akce "Připojit se"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Analyzovat akci JSON

Chcete-li odkazovat na vlastnosti nebo získat přístup k vlastnostem v obsahu zápisu objektu JavaScriptu (JSON), můžete vytvořit uživatelsky přívětivá pole nebo tokeny pro tyto vlastnosti pomocí akce **Analyzovat JSON.** Tímto způsobem můžete vybrat tyto vlastnosti ze seznamu dynamického obsahu při zadávání vstupů pro aplikaci logiky. Pro tuto akci můžete buď poskytnout schéma JSON nebo generovat schéma JSON z ukázkového obsahu JSON nebo datové části.

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad **Analýzy JSON** a **Inicializovat** definice proměnných z tohoto článku do základní definice pracovního postupu vlastní aplikace logiky: [Příklady kódu operace data - Analyzovat JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a akce **proměnné Initialize.** Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je objekt JSON, který má vlastnosti a hodnoty. Když později otestujete aplikaci logiky, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   ![Spuštění ukázkové aplikace logiky pro akci "Analyzovat JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. V aplikaci logiky, kde chcete analyzovat obsah JSON, postupujte jedním z následujících kroků:

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Vyberte akci "Nový krok" pro akci "Analyzovat JSON".](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole `parse json` zadejte jako filtr. Ze seznamu akcí vyberte akci **Analyzovat JSON.**

   ![Vybrat akci "Analyzovat JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. V poli **Obsah** zadejte obsah JSON, který chcete analyzovat.

   V tomto příkladu se po klepnutí do pole **Obsah** zobrazí seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vyberte objekt JSON pro akci JSON analýzy.](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Zadejte schéma JSON, které popisuje obsah JSON, který analyzujete.

   V tomto příkladu je zde schéma JSON:

   ![Zadejte schéma JSON pro objekt JSON, který chcete analyzovat](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Pokud nemáte schéma, můžete generovat toto schéma z obsahu JSON nebo datové *části*, které jsou analýzy. 
   
   1. V akci **Analýza JSON** vyberte **použít ukázkovou datovou část ke generování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**, zadejte obsah JSON a pak vyberte **Hotovo**.

      ![Zadejte obsah JSON pro generování schématu.](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Analýza akce JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li ověřit, zda **akce Parse JSON** vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce **Parse JSON.**

1. V aplikaci logiky přidejte akci, která vám může odeslat výsledky akce **Analyzovat JSON.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Když se otevře seznam dynamického obsahu, v rámci akce **Analyzovat JSON,** můžete nyní vybrat vlastnosti z analyzovaného obsahu JSON.

   Tento příklad používá akci **Odeslat e-mail** v Outlooku Office 365 a do textu e-mailu obsahuje pole **Jméno**, **Příjmení**a **E-mail:**

   ![Vlastnosti JSON v akci Odeslat e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Zde je hotová e-mailová akce:

   ![Dokončený příklad pro akci e-mailu](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**. 

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky akce "Analyzovat JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Vybrat akci

Chcete-li vytvořit pole, ve které jsou objekty JSON sestavené z hodnot v existujícím poli, použijte akci **Select.** Můžete například vytvořit objekt JSON pro každou hodnotu v poli celého čísla zadáním vlastností, které musí mít každý objekt JSON, a jak namapovat hodnoty ve zdrojovém poli na tyto vlastnosti. A i když můžete změnit součásti v těchto objektech JSON, výstupní pole má vždy stejný počet položek jako zdrojové pole.

> [!NOTE]
> Pro akce, které mají použít výstup pole z akce **Select,** musí tyto akce přijmout pole jako vstup, nebo budete muset transformovat výstupní pole do jiného kompatibilního formátu. 

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad **Vybrat** a **inicializovat** definice akcí proměnných z tohoto článku do základní definice pracovního postupu vlastní aplikace [logiky: Příklady kódu operace data - Vyberte](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá portál Azure a aplikaci logiky s aktivační událostí **opakování** a akce **proměnné Initialize.** Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které má některé ukázkové celá čísla. Když později otestujete aplikaci logiky, můžete ručně spustit aplikaci bez čekání na spuštění aktivační události.

   ![Spuštění ukázkové aplikace logiky pro akci "Vybrat"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. V aplikaci logiky, kde chcete vytvořit pole, postupujte jedním z následujících kroků: 

   * Chcete-li přidat akci pod posledním krokem, vyberte **Nový krok**.

     ![Vyberte akci "Nový krok" pro akci "Vybrat".](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši**+** na spojovací šipku, aby se znaménko plus ( ) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `select` zadejte jako filtr. Ze seznamu akcí vyberte akci **Vybrat.**

   ![Vyberte akci "Vybrat"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. V poli **Od** zadejte požadované zdrojové pole.

   V tomto příkladu se po klepnutí do pole **Od** zobrazí seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vybrat zdrojové pole pro akci Vybrat](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. V levém sloupci pole **Map** zadejte název vlastnosti, kterou chcete přiřadit každou hodnotu ve zdrojovém poli. V pravém sloupci zadejte výraz, který představuje hodnotu, kterou chcete přiřadit vlastnost.

   Tento příklad určuje "Product_ID" jako název vlastnosti přiřadit každou hodnotu `item()` v poli celé číslo pomocí funkce ve výrazu, který přistupuje ke každé položce pole. 

   ![Určení vlastnosti a hodnot objektu JSON pro vytvoření pole](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Zde je hotová akce:

   ![Dokončený příklad akce "Vybrat"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v definici základního pracovního postupu naleznete v [tématu Výběr akce](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li **ověřit,** zda akce Vybrat vytvoří očekávané výsledky, odešlete si oznámení, které obsahuje výstup z akce **Vybrat.**

1. V aplikaci logiky přidejte akci, která vám může poslat výsledky z akce **Vybrat.**

1. V této akci klikněte na libovolné místo, kde se mají výsledky zobrazit. Po otevření seznamu dynamického obsahu vyberte **výraz**. Chcete-li získat výstup pole z akce **Vybrat,** zadejte tento výraz, který obsahuje název akce **Vybrat:**

   `@actionBody('Select')`

   Tento příklad používá akci **Odeslat e-mail** v Outlooku `@actionBody('Select')` Office 365 a zahrnuje výstupy z výrazu v textu e-mailu:

   ![Výstupy akce z akce "Vybrat"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Teď ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   Na základě e-mailového konektoru, který jste použili, získáte výsledky:

   ![E-mail s výsledky akce "Vybrat"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech Logic Apps](../connectors/apis-list.md)
