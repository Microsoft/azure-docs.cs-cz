---
title: Provádění operací s daty
description: Převod, Správa a manipulace výstupů a formátů dat v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710367"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Provádění operací s daty v Azure Logic Apps

Tento článek ukazuje, jak můžete pracovat s daty v aplikacích logiky přidáním akcí pro tyto úlohy a dalších akcí:

* Vytvoří tabulky z polí.
* Vytvořte pole z jiných polí na základě podmínky.
* Vytvořte uživatelsky přívětivé tokeny z vlastností objektu JavaScript Object Notation (JSON), abyste tyto vlastnosti mohli snadno použít ve svém pracovním postupu.

Pokud tu požadovanou akci nenajdete, zkuste procházet mnoho různých [funkcí manipulace s daty](../logic-apps/workflow-definition-language-functions-reference.md) , které Azure Logic Apps poskytuje.

Tyto tabulky shrnují datové operace, které lze použít a jsou uspořádány na základě typů zdrojových dat, na kterých operace fungují, ale každý popis se zobrazí abecedně.

**Akce pole** 

Tyto akce vám pomůžou pracovat s daty v polích.

| Akce | Popis |
|--------|-------------|
| [**Vytvořit tabulku CSV**](#create-csv-table-action) | Vytvoří tabulku hodnot s oddělovači (CSV) z pole. |
| [**Vytvořit tabulku HTML**](#create-html-table-action) | Vytvoří tabulku HTML z pole. |
| [**Filtrovat pole**](#filter-array-action) | Vytvořte podmnožinu pole z pole na základě zadaného filtru nebo podmínky. |
| [**Spojit**](#join-action) | Vytvoří řetězec ze všech položek v poli a oddělí každou položku zadaným znakem. |
| [**Vyberte**](#select-action) | Vytvoří pole z určených vlastností pro všechny položky v jiném poli. |
||| 

**Akce JSON**

Tyto akce vám pomůžou pracovat s daty ve formátu JavaScript Object Notation (JSON).

| Akce | Popis |
|--------|-------------|
| [**Vytvořit**](#compose-action) | Vytvoření zprávy nebo řetězce z více vstupů, které mohou mít různé datové typy. Tento řetězec je pak možné použít jako jeden vstup místo opakovaného zadávání stejných vstupů. Můžete například vytvořit jednu zprávu JSON z různých vstupů. |
| [**Analyzovat JSON**](#parse-json-action) | Vytvářejte uživatelsky přívětivé datové tokeny pro vlastnosti v obsahu JSON, abyste mohli snadněji používat vlastnosti ve svých aplikacích logiky. |
|||

Chcete-li vytvořit složitější transformace JSON, přečtěte si téma [provádění pokročilých TRANSFORMACÍ JSON pomocí tekutých šablon](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, kde potřebujete operaci pro práci s daty

  Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve vaší aplikaci logiky 

  Datové operace jsou k dispozici pouze jako akce, takže před použitím těchto akcí spusťte aplikaci logiky s triggerem a zahrňte všechny další akce potřebné pro vytvoření požadovaných výstupů.

<a name="compose-action"></a>

## <a name="compose-action"></a>Akce psaní

Chcete-li vytvořit jeden výstup, jako je například objekt JSON z více vstupů, můžete použít akci **vytvořit** . Vaše vstupy mohou mít různé typy, jako jsou celá čísla, logické hodnoty, pole, objekty JSON a jakýkoli jiný nativní typ, který Azure Logic Apps podporuje, například binary a XML. Pak můžete použít výstup v akcích, které následují po akci **psaní** . Akce **napsat** vám také může ušetřit při vytváření pracovního postupu aplikace logiky opakovaně při zadávání stejných vstupů.

Můžete například vytvořit zprávu JSON z více proměnných, jako například řetězcové proměnné, které ukládají křestní jména a příjmení lidí, a celočíselnou proměnnou, která ukládá věkové jméno uživatele. V tomto případě akce **psaní** akceptuje tyto vstupy:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

a vytvoří tento výstup:

`{"age":35,"fullName":"Owens,Sophie"}`

Pokud chcete vyzkoušet příklad, postupujte podle těchto kroků pomocí návrháře aplikace logiky. Pokud ale dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad definice akcí **vytvořit** a **inicializovat proměnnou** z tohoto článku do své vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operace s daty – sestavení](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a několika akcimi **inicializovat proměnnou** . Tyto akce jsou nastaveny pro vytváření dvou řetězcových proměnných a celočíselné proměnné. Když později otestujete aplikaci logiky, můžete aplikaci spustit ručně bez čekání na aktivaci triggeru.

   ![Spouští se ukázková aplikace logiky pro akci psaní.](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. V aplikaci logiky, kde chcete vytvořit výstup, postupujte podle jednoho z následujících kroků: 

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Pro akci psaní vyberte nový krok.](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole `compose` jako filtr. V seznamu akce vyberte akci **vytvořit** .

   ![Vybrat akci psaní](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Do pole **vstupy** zadejte vstupy, které chcete pro vytvoření výstupu.

   Pokud v tomto příkladu kliknete do pole **vstupy** , zobrazí se seznam dynamického obsahu, abyste mohli vybrat dříve vytvořené proměnné:

   ![Vyberte vstupy, které se mají použít pro akci psaní.](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Tady je příklad akce **psaní** , kterou jste dokončili: 

   ![Příklad dokončení pro akci "sestavit"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v příslušné definici pracovního postupu najdete v tématu [akce psaní](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **Vytvoření** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **psaní** .

1. Ve vaší aplikaci logiky přidejte akci, která vám umožní odeslat výsledky z akce **psaní** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah vyberte v části akce **psaní** možnost **výstup**.

   V tomto příkladu se používá akce **Odeslat e-mail** a obsahuje **výstupní** pole v textu e-mailu a subjektu:

   ![Pole "Output" (výstup) pro akci "sestavit"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky akce "vytvořit"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Akce vytvoření tabulky CSV

Pokud chcete vytvořit tabulku hodnot s oddělovači (CSV), která obsahuje vlastnosti a hodnoty z objektů JavaScript Object Notation (JSON) v poli, použijte akci **vytvořit tabulku CSV** . Výslednou tabulku pak můžete použít v akcích, které následují po akci **vytvořit tabulku CSV** .

Pokud upřednostňujete práci v editoru zobrazení kódu, můžete zkopírovat příklad **Vytvoření tabulky CSV** a inicializovat definice akcí **proměnných** z tohoto článku do vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operace s daty – vytvoření tabulky CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a akcí **inicializovat proměnnou** . Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které obsahuje některé vlastnosti a hodnoty ve formátu JSON. Když později otestujete aplikaci logiky, můžete aplikaci spustit ručně bez čekání na aktivaci triggeru.

   ![Spouští se ukázková aplikace logiky pro akci vytvoření tabulky CSV.](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. V aplikaci logiky, ve které chcete vytvořit tabulku CSV, proveďte jeden z následujících kroků: 

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Pro akci vytvoření tabulky CSV vyberte nový krok.](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole `create csv table` jako filtr. V seznamu akce vyberte akci **vytvořit tabulku CSV** .

   ![Výběr akce vytvoření tabulky CSV](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Do pole **od** zadejte pole nebo výraz, který chcete pro vytvoření tabulky.

   V tomto příkladu, když kliknete dovnitř pole **z** , se zobrazí seznam dynamický obsah, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vybrat výstup pole pro vytvoření tabulky CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Chcete-li vytvořit uživatelsky přívětivé tokeny pro vlastnosti v objektech JSON, abyste je mohli vybrat jako vstupy, použijte [analýzu JSON](#parse-json-action) před voláním akce **vytvořit tabulku CSV** .

   Tady je příklad dokončené akce **Vytvoření tabulky CSV** : 

   ![Příklad dokončené akce vytvoření tabulky CSV](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="customize-table-format"></a>Přizpůsobení formátu tabulky

Ve výchozím nastavení je vlastnost **Columns** nastavena na hodnotu automaticky vytvořit sloupce tabulky založené na položkách pole. Chcete-li zadat vlastní hlavičky a hodnoty, postupujte takto:

1. Otevřete seznam **sloupce** a vyberte možnost **vlastní**.

1. Ve vlastnosti **záhlaví** určete vlastní text záhlaví, který chcete použít místo toho.

1. Do vlastnosti **hodnota** zadejte vlastní hodnotu, která se má použít místo toho.

Chcete-li vrátit hodnoty z pole, můžete použít [ `item()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#item) s akcí **vytvořit tabulku CSV** . Ve `For_each` smyčce můžete [ `items()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#items)použít.

Předpokládejme například, že chcete sloupce tabulky, které mají pouze hodnoty vlastností, a nikoli názvy vlastností z pole. Chcete-li vrátit pouze tyto hodnoty, postupujte podle těchto kroků pro práci v zobrazení návrháře nebo v zobrazení kódu. Zde je výsledek, který tento příklad vrátí:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Práce v zobrazení návrháře

V akci ponechte sloupec **záhlaví** prázdný. Na každém řádku ve sloupci **hodnota** odkažte na každou vlastnost pole, kterou chcete. Každý řádek pod **hodnotou** vrátí všechny hodnoty pro zadanou vlastnost pole a v tabulce se bude jednat o sloupec.

1. V části **hodnota**na každém řádku, který chcete, klikněte do pole pro úpravy, aby se zobrazil seznam dynamického obsahu.

1. V seznamu dynamický obsah vyberte možnost **výraz**.

1. V editoru výrazů zadejte tento výraz, který určuje hodnotu vlastnosti pole, kterou chcete, a vyberte **OK**.

   `item()?['<array-property-name>']`

   Například:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereference "Popis" pro "vytvoření tabulky CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Předchozí kroky opakujte pro každou vlastnost pole, kterou požadujete. Až to budete mít, vaše akce bude vypadat jako v tomto příkladu:

   ![funkce Item () v části vytvořit tabulku CSV](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Chcete-li přeložit výrazy na více popisných verzí, přepněte do zobrazení kódu a zpět do zobrazení návrháře a potom znovu otevřete sbalenou akci:

   Akce **vytvořit tabulku CSV** se teď jeví jako v tomto příkladu:

   !["Vytvoření tabulky CSV" – vyřešené výrazy, žádná záhlaví](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Práce v zobrazení kódu

V definici JSON akce `columns` nastavte v poli `header` vlastnost na prázdný řetězec. Pro každou `value` vlastnost odkažte na každou vlastnost pole, kterou chcete.

1. Na panelu nástrojů návrháře vyberte **zobrazení kódu**.

1. V editoru kódu v `columns` poli Akce přidejte `header` vlastnost Empty a tento `value` výraz pro každý sloupec hodnot pole, který chcete použít:

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

1. Přepněte zpátky na zobrazení návrháře a znovu otevřete sbalenou akci.

   Akce **vytvořit tabulku CSV** se teď jeví jako v tomto příkladu a výrazy se vyřešily na podrobnější verze:

   !["Vytvoření tabulky CSV" – vyřešené výrazy a žádná záhlaví](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Další informace o této akci v příslušné definici pracovního postupu najdete v tématu [Akce tabulky](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **Vytvoření tabulky CSV** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **Vytvoření tabulky CSV** .

1. Ve vaší aplikaci logiky přidejte akci, která vám umožní odeslat výsledky z akce **vytvořit tabulku CSV** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah vyberte v akci **vytvořit tabulku CSV** možnost **výstup**. 

   V tomto příkladu se používá **e-mailová** akce Office 365 Outlooku poslat e-mail a obsahuje pole **výstup** do textu e-mailu:

   ![Pole "výstup" pro akci "vytvořit tabulku CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky akce "vytvořit tabulku CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Akce vytvoření tabulky HTML

Chcete-li vytvořit tabulku HTML, která obsahuje vlastnosti a hodnoty z objektů JavaScript Object Notation (JSON) v poli, použijte akci **vytvořit tabulku HTML** . Výslednou tabulku pak můžete použít v akcích, které následují po akci **Vytvoření tabulky HTML** .

Pokud upřednostňujete práci v editoru zobrazení kódu, můžete zkopírovat příklad **Vytvoření tabulky HTML** a inicializovat definice akcí **proměnných** z tohoto článku do vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operace s daty – vytvoření tabulky HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a akcí **inicializovat proměnnou** . Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které obsahuje některé vlastnosti a hodnoty ve formátu JSON. Když později otestujete aplikaci logiky, můžete aplikaci spustit ručně bez čekání na aktivaci triggeru.

   ![Spouští se ukázková aplikace logiky pro vytvoření tabulky HTML.](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. V aplikaci logiky, kde chcete vytvořit tabulku HTML, proveďte jeden z následujících kroků:

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Pro akci vytvoření tabulky HTML vyberte nový krok.](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole `create html table` jako filtr. V seznamu akce vyberte akci **vytvořit tabulku HTML** .

   ![Výběr akce vytvoření tabulky HTML](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Do pole **od** zadejte pole nebo výraz, který chcete pro vytvoření tabulky.

   V tomto příkladu, když kliknete dovnitř pole **z** , se zobrazí seznam dynamický obsah, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vybrat výstup pole pro vytvoření tabulky HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Chcete-li vytvořit uživatelsky přívětivé tokeny pro vlastnosti v objektech JSON, aby bylo možné vybrat tyto vlastnosti jako vstupy, použijte [analýzu JSON](#parse-json-action) před voláním akce **vytvořit tabulku HTML** .

   Tady je příklad dokončené akce **Vytvoření tabulky HTML** :

   ![Příklad dokončené pro "vytvoření tabulky HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="customize-table-format"></a>Přizpůsobení formátu tabulky

Ve výchozím nastavení je vlastnost **Columns** nastavena na hodnotu automaticky vytvořit sloupce tabulky založené na položkách pole. Chcete-li zadat vlastní hlavičky a hodnoty, postupujte takto:

1. Otevřete seznam **sloupce** a vyberte možnost **vlastní**.

1. Ve vlastnosti **záhlaví** určete vlastní text záhlaví, který chcete použít místo toho.

1. Do vlastnosti **hodnota** zadejte vlastní hodnotu, která se má použít místo toho.

Chcete-li vrátit hodnoty z pole, můžete použít [ `item()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#item) s akcí **vytvořit tabulku HTML** . Ve `For_each` smyčce můžete [ `items()` funkci](../logic-apps/workflow-definition-language-functions-reference.md#items)použít.

Předpokládejme například, že chcete sloupce tabulky, které mají pouze hodnoty vlastností, a nikoli názvy vlastností z pole. Chcete-li vrátit pouze tyto hodnoty, postupujte podle těchto kroků pro práci v zobrazení návrháře nebo v zobrazení kódu. Zde je výsledek, který tento příklad vrátí:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Práce v zobrazení návrháře

V akci ponechte sloupec **záhlaví** prázdný. Na každém řádku ve sloupci **hodnota** odkažte na každou vlastnost pole, kterou chcete. Každý řádek pod **hodnotou** vrátí všechny hodnoty pro zadanou vlastnost a v tabulce se bude jednat o sloupec.

1. V části **hodnota**na každém řádku, který chcete, klikněte do pole pro úpravy, aby se zobrazil seznam dynamického obsahu.

1. V seznamu dynamický obsah vyberte možnost **výraz**.

1. V editoru výrazů zadejte tento výraz, který určuje hodnotu vlastnosti pole, kterou chcete, a vyberte **OK**.

   `item()?['<array-property-name>']`

   Například:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Vlastnost dereference v akci "vytvořit tabulku HTML"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Předchozí kroky opakujte pro každou vlastnost pole, kterou požadujete. Až to budete mít, vaše akce bude vypadat jako v tomto příkladu:

   ![funkce Item () v části "vytvoření tabulky HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Chcete-li přeložit výrazy na více popisných verzí, přepněte do zobrazení kódu a zpět do zobrazení návrháře a potom znovu otevřete sbalenou akci:

   Akce **vytvořit tabulku HTML** teď vypadá jako v tomto příkladu:

   !["Vytvoření tabulky HTML" – vyřešené výrazy, žádná záhlaví](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Práce v zobrazení kódu

V definici JSON akce `columns` nastavte v poli `header` vlastnost na prázdný řetězec. Pro každou `value` vlastnost odkažte na každou vlastnost pole, kterou chcete.

1. Na panelu nástrojů návrháře vyberte **zobrazení kódu**.

1. V editoru kódu v `columns` poli Akce přidejte `header` vlastnost Empty a tento `value` výraz pro každý sloupec hodnot pole, který chcete použít:

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

1. Přepněte zpátky na zobrazení návrháře a znovu otevřete sbalenou akci.

   Akce **vytvořit tabulku HTML** teď vypadá jako v tomto příkladu a výrazy se vyřešily na podrobnější verze:

   !["Vytvoření tabulky HTML" – vyřešené výrazy a žádná záhlaví](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Další informace o této akci v příslušné definici pracovního postupu najdete v tématu [Akce tabulky](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **Vytvoření tabulky HTML** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **Vytvoření tabulky HTML** .

1. Ve vaší aplikaci logiky přidejte akci, která vám umožní odeslat výsledky z akce **Vytvoření tabulky HTML** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah vyberte v akci **vytvořit tabulku HTML** možnost **výstup**. 

   V tomto příkladu se používá **e-mailová** akce Office 365 Outlooku poslat e-mail a obsahuje pole **výstup** do textu e-mailu:

   ![Pole "výstup" pro "vytvoření tabulky HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Pokud zahrnete výstup HTML tabulky do e-mailové akce, ujistěte se, že jste v rozšířené možnosti e-mailové akce nastavili vlastnost **is HTML** na **Ano** . Tímto způsobem akce e-mailu naformátuje správně tabulku HTML.

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky "vytvoření tabulky HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Akce filtru pole

Chcete-li vytvořit menší pole, které obsahuje položky, které splňují určitá kritéria, z existujícího pole, použijte akci **filtrování pole** . Filtrované pole pak můžete použít v akcích, které následují po akci **filtru pole** .

> [!NOTE]
> Libovolný text filtru, který používáte ve vaší podmínce, rozlišuje velká a malá písmena. Tato akce také nemůže změnit formát nebo součásti položek v poli. 
> 
> Pro akce, které mají použít výstup pole z akce **filtrovat pole** , musí buď tyto akce přijmout pole jako vstup, nebo může být nutné transformovat výstupní pole do jiného kompatibilního formátu.
> 
> Pokud voláte koncový bod HTTP a dostanete odpověď JSON, použijte akci **analyzovat JSON** pro zpracování odpovědi JSON. 
> V opačném případě může akce **pole filtru** číst pouze tělo odpovědi a nikoli strukturu datové části JSON.

Pokud upřednostňujete práci v editoru zobrazení kódu, můžete zkopírovat příklad **pole filtru** a inicializovat definice akcí **proměnných** z tohoto článku do své vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operací s daty – pole filtru](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a akcí **inicializovat proměnnou** . Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které obsahuje některá vzorová celá čísla. Když později otestujete aplikaci logiky, můžete aplikaci spustit ručně bez čekání na aktivaci triggeru.

   > [!NOTE]
   > I když tento příklad používá jednoduché pole typu Integer, je tato akce obzvláště užitečná pro pole objektů JSON, kde můžete filtrovat podle vlastností a hodnot objektů.

   ![Spouští se ukázková aplikace logiky pro akci "Filter Array".](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. V aplikaci logiky, kde chcete vytvořit filtrované pole, proveďte jeden z následujících kroků: 

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Pro akci filtrování pole vyberte nový krok.](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte `filter array` jako filtr. V seznamu akce vyberte akci **Filtr pole** .

   ![Výběr akce filtrovat pole](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Do pole **od** zadejte pole nebo výraz, který chcete filtrovat.

   V tomto příkladu, když kliknete dovnitř pole **z** , se zobrazí seznam dynamický obsah, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vyberte výstup pole pro vytvoření filtrovaného pole.](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Pro podmínku zadejte položky pole, které chcete porovnat, vyberte operátor porovnání a zadejte hodnotu porovnání.

   V tomto příkladu se používá `item()` funkce pro přístup k jednotlivým položkám v poli, zatímco akce **pole filtru** vyhledává položky pole, jejichž hodnota je větší než jedna:

   ![Příklad dokončení pro akci "Filter Array"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [Akce dotazu](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **pole filtru** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **filtrovat pole** .

1. V aplikaci logiky přidejte akci, která vám umožní odeslat výsledky z akce **filtrovat pole** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah vyberte **výraz**. Chcete-li získat výstup pole z akce **filtrování pole** , zadejte tento výraz, který obsahuje název akce **pole filtru** :

   `@actionBody('Filter_array')`

   V tomto příkladu se používá akce **Odeslat e-mail** pro Office 365 Outlook a obsahuje výstupy z výrazu **actionBody (' Filter_array ')** v těle e-mailu:

   ![Výstupy akcí z akce filtrovat pole](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky akce filtrovat pole](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Akce JOIN

Chcete-li vytvořit řetězec, který má všechny položky z pole a oddělit tyto položky pomocí konkrétního znaku oddělovače, použijte akci **spojení** . Pak můžete použít řetězec v akcích, které následují po akci **spojení** .

Pokud upřednostňujete práci v editoru zobrazení kódu, můžete zkopírovat příklad definice akcí **spojování** a **inicializovat proměnnou** z tohoto článku do své vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operací s daty – spojení](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a akcí **inicializovat proměnnou** . Tato akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které obsahuje některá vzorová celá čísla. Když testujete aplikaci logiky později, můžete aplikaci logiky spustit ručně, aniž byste čekali na požár triggeru.

   ![Spouští se ukázková aplikace logiky pro akci JOIN.](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. V aplikaci logiky, kde chcete vytvořit řetězec z pole, proveďte jeden z následujících kroků:

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![SSelect akci "nový krok" pro "spojení"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte `join` jako filtr. V seznamu akce vyberte tuto akci: **připojit**

   ![Vybrat akci spojení](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Do pole **od** zadejte pole, které obsahuje položky, které chcete připojit jako řetězec.

   V tomto příkladu když kliknete dovnitř pole **z** , zobrazí se seznam dynamického obsahu, ve kterém můžete vybrat dříve vytvořenou proměnnou:  

   ![Vybrat výstup pole pro vytvoření řetězce](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Do pole **spojit s** zadejte znak, který chcete oddělit o každou položku pole. 

   V tomto příkladu se používá dvojtečka (:) jako oddělovač.

   ![Zadejte znak oddělovače.](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v [akci spojení](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **spojení** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **Join** .

1. Ve vaší aplikaci logiky přidejte akci, která vám umožní odeslat výsledky akce **Join** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah vyberte v akci **připojit** možnost **výstup**. 

   V tomto příkladu se používá **e-mailová** akce Office 365 Outlooku poslat e-mail a obsahuje pole **výstup** do textu e-mailu:

   ![Pole "výstup" pro akci "Join"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky akce JOIN](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Analyzovat akci JSON

Chcete-li odkazovat nebo přistupovat k vlastnostem v obsahu JavaScript Object Notation (JSON), můžete pro tyto vlastnosti vytvořit uživatelsky přívětivá pole nebo tokeny pomocí akce **analyzovat JSON** . Tímto způsobem můžete vybrat tyto vlastnosti ze seznamu dynamického obsahu, když zadáte vstupy pro vaši aplikaci logiky. Pro tuto akci můžete buď zadat schéma JSON, nebo vygenerovat schéma JSON z ukázkového obsahu nebo datové části JSON.

Pokud upřednostňujete práci v editoru zobrazení kódu, můžete zkopírovat příklad definice akcí pro **analýzu JSON** a **inicializovat proměnnou** z tohoto článku do své vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operace s daty – analýza kódu JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a akcí **inicializovat proměnnou** . Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je objekt JSON, který obsahuje vlastnosti a hodnoty. Když později otestujete aplikaci logiky, můžete aplikaci spustit ručně bez čekání na aktivaci triggeru.

   ![Spouští se ukázková aplikace logiky pro akci analyzovat JSON.](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. V aplikaci logiky, kde chcete analyzovat obsah JSON, postupujte podle jednoho z následujících kroků:

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Pro akci analyzovat JSON vyberte nový krok.](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte `parse json` jako filtr. V seznamu akce vyberte akci **analyzovat JSON** .

   ![Vybrat akci analyzovat JSON](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. V poli **obsah** zadejte obsah JSON, který chcete analyzovat.

   Pokud v tomto příkladu kliknete do pole **obsahu** , zobrazí se seznam dynamického obsahu, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vyberte objekt JSON pro akci analyzovat JSON.](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Zadejte schéma JSON, které popisuje obsah JSON, který budete analyzovat.

   V tomto příkladu je tady schéma JSON:

   ![Poskytněte schéma JSON pro objekt JSON, který chcete analyzovat.](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Pokud schéma nemáte, můžete toto schéma vygenerovat z obsahu JSON nebo *datové části*, kterou analyzujete. 
   
   1. V akci **analyzovat JSON** vyberte **použít ukázkovou datovou část k vygenerování schématu**.

   1. V části **Zadejte nebo vložte ukázkovou datovou část JSON**zadejte obsah JSON a potom vyberte **Hotovo**.

      ![Zadejte obsah JSON pro generování schématu.](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [analýza akce JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **analýzy JSON** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **analyzovat JSON** .

1. V aplikaci logiky přidejte akci, která vám umožní odeslat výsledky z akce **analyzovat JSON** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah v rámci akce **analyzovat JSON** teď můžete vybrat vlastnosti z analyzovaného obsahu JSON.

   V tomto příkladu se používá **e-mailová** akce Office 365 Outlooku odeslat e-mail a do těla e-mailu zahrne pole **FirstName**, **LastName**a **e-mail** :

   ![Vlastnosti JSON v akci odeslat e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Toto je dokončená e-mailová akce:

   ![Příklad dokončené akce e-mailu](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**. 

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky akce analyzovat JSON](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Vybrat akci

Chcete-li vytvořit pole, které obsahuje objekty JSON sestavené z hodnot v existujícím poli, použijte akci **Vybrat** . Můžete například vytvořit objekt JSON pro každou hodnotu v celočíselném poli zadáním vlastností, které musí mít každý objekt JSON, a jak mapovat hodnoty ve zdrojovém poli na tyto vlastnosti. A i když můžete změnit komponenty v těchto objektech JSON, výstupní pole má vždycky stejný počet položek jako zdrojové pole.

> [!NOTE]
> Pro akce, které mají použít výstup pole z akce **Select** , musí buď tyto akce přijmout pole jako vstup, nebo může být nutné transformovat výstupní pole do jiného kompatibilního formátu. 

Pokud dáváte přednost práci v editoru zobrazení kódu, můžete zkopírovat příklad definice akcí pro **Výběr** a **inicializaci proměnných** z tohoto článku do své vlastní definice pracovního postupu aplikace logiky: [Příklady kódu operace s daty – výběr](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se používá Azure Portal a aplikace logiky s triggerem **opakování** a akcí **inicializovat proměnnou** . Akce je nastavena pro vytvoření proměnné, jejíž počáteční hodnota je pole, které obsahuje některá vzorová celá čísla. Když později otestujete aplikaci logiky, můžete aplikaci spustit ručně bez čekání na aktivaci triggeru.

   ![Spouští se ukázková aplikace logiky pro akci vybrat.](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. V aplikaci logiky, kde chcete vytvořit pole, proveďte jeden z následujících kroků: 

   * Pokud chcete v posledním kroku přidat akci, vyberte **Nový krok**.

     ![Pro akci vybrat vyberte nový krok.](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku připojení, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `select` jako filtr. V seznamu akce vyberte akci **Vybrat** .

   ![Vyberte akci vybrat.](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Do pole **od** zadejte zdrojové pole, které chcete.

   V tomto příkladu, když kliknete dovnitř pole **z** , se zobrazí seznam dynamický obsah, abyste mohli vybrat dříve vytvořenou proměnnou:

   ![Vybrat zdrojové pole pro akci výběru](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. V levém sloupci v poli **Mapa** zadejte název vlastnosti, který chcete přiřadit každé hodnotě ve zdrojovém poli. Do sloupce na pravé straně zadejte výraz, který představuje hodnotu, kterou chcete přiřadit vlastnost.

   Tento příklad určuje "Product_ID" jako název vlastnosti pro přiřazení každé hodnoty v poli s celými čísly pomocí `item()` funkce ve výrazu, který přistupuje k jednotlivým položkám pole. 

   ![Určení vlastností objektu JSON a hodnot pro vytvoření pole](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Toto je dokončená akce:

   ![Příklad dokončení pro akci "vybrat"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [Výběr akce](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete potvrdit, jestli akce **Select** vytvoří očekávané výsledky, pošlete sami sobě oznámení, které obsahuje výstup z akce **Vybrat** .

1. Ve vaší aplikaci logiky přidejte akci, která vám umožní odeslat výsledky z akce **Vybrat** .

1. V takovém případě klikněte na libovolné místo, kde chcete zobrazit výsledky. Po otevření seznamu dynamický obsah vyberte **výraz**. Chcete-li získat výstup pole z akce **Select** , zadejte tento výraz, který obsahuje název akce **výběru** :

   `@actionBody('Select')`

   V tomto příkladu se používá akce **Odeslat e-mail** pro Office 365 Outlook a obsahuje výstupy z `@actionBody('Select')` výrazu v těle e-mailu:

   ![Výstupy akcí z akce "vybrat"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Nyní ručně spusťte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Spustit**.

   V závislosti na e-mailovém konektoru, který jste použili, získáte následující výsledky:

   ![E-mail s výsledky akce "vybrat"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech Logic Apps](../connectors/apis-list.md)
