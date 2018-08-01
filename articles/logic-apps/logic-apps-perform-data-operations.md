---
title: Provádění operací s daty – Azure Logic Apps | Dokumentace Microsoftu
description: Převod, spravovat a zpracování dat výstupy a formáty v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 7e62986569888ebbcd9f17b4eb4cfb2c70411d4a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392079"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Provádět operace s daty v Azure Logic Apps

Tento článek popisuje, jak můžete pracovat s daty ve svých aplikacích logiky přidáním akce pro tyto a další úkoly:

* Vytváření tabulek z polí.
* Vytvoří pole z jiných polí na základě podmínky.
* Vytvořte uživatelsky přívětivé tokeny z vlastností objektu JavaScript Object Notation (JSON), takže tyto vlastnosti můžete snadno použít ve svém pracovním postupu.

Pokud nenajdete akce, kterou chcete tady, zkusit mnoho různých [funkce pro zpracování dat](../logic-apps/workflow-definition-language-functions-reference.md) poskytující Logic Apps. 

Tyto tabulky shrnují data, která operací můžete použít a jsou uspořádané podle zdrojové datové typy, které operace pracovat, ale každý popis se zobrazí podle abecedy.

**Pole akcí** 

Tato akce vám pomohou při práci s daty v polích.

| Akce | Popis | 
|--------|-------------| 
| [**Vytvoření tabulky CSV**](#create-csv-table-action) | Vytvořte tabulku hodnotami oddělenými čárkami (CSV) z pole. | 
| [**Vytvoření tabulky HTML**](#create-html-table-action) | Vytvoření tabulky HTML z pole. | 
| [**Filtrování pole**](#filter-array-action) | Vytvoření podsadě pole z pole na základě zadaného filtru nebo podmínky. | 
| [**Připojte se k**](#join-action) | Vytvoření řetězce z všechny položky v poli a jednotlivé položky oddělte zadaný znak. | 
| [**Vyberte**](#select-action) | Vytvoří pole z zadané vlastnosti pro všechny položky v jiné pole. | 
||| 

**Akce JSON**

Tato akce vám pomohou při práci s daty ve formátu JavaScript Object Notation (JSON).

| Akce | Popis | 
|--------|-------------| 
| [**Compose**](#compose-action) | Vytvoření zprávy, nebo řetězec, z více vstupů, které mohou mít různé datové typy. Pak můžete použít tento řetězec jako jeden vstup, nikoli opakovaně zadávat stejné vstupy. Můžete například vytvořit do jedné zprávy JSON z různých vstupy. | 
| [**Parsování formátu JSON**](#parse-json-action) | Vytvořte uživatelsky přívětivé data tokeny pro vlastnosti ve formátu JSON obsahu tak můžete snadno použít vlastnosti ve svých aplikacích logiky. | 
||| 

Chcete-li vytvořit složitější transformace JSON, naleznete v tématu [provádět pokročilé transformacích JSON pomocí Liquid šablony](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Požadavky

V příkladech v tomto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

* Aplikace logiky, kde je třeba operace pro práci s daty 

  Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve své aplikaci logiky 

  Operace s daty jsou k dispozici pouze jako akce, tak, aby před použitím těchto akcí spuštění aplikace logiky s triggerem a všechny další akce, které jsou potřebné pro vytváření výstupy, které chcete zahrnout.

<a name="compose-action"></a>

## <a name="compose-action"></a>Akce psaní

Chcete-li vytvořit jeden výstupu, jako je objekt JSON z více vstupů, můžete použít **operace s daty – psaní** akce. Vstupy může mít různé typy, jako například celá čísla, logické hodnoty, pole, objekty JSON a jakékoli jiné nativní typ, který podporuje Azure Logic Apps, například binární a XML. Pak můžete výstup v akce, které následují po **Compose** akce. **Compose** akcí můžete také uložit můžete opakovaně zadávání stejnými vstupy při vytvoření pracovního postupu aplikace logiky. 

Například můžete vytvořit zprávu JSON z více proměnných, jako jsou proměnné řetězce, které obsahují jména a příjmení lidí a celočíselnou proměnnou, která ukládá lidí ve věku. Tady **Compose** přijímá tyto vstupy akce:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

a vytvoří tento výstup:

`{"age":35,"fullName":"Owens,Sophie"}`

Pokud chcete vyzkoušet příklad, následujícím postupem pomocí návrháře aplikace logiky. Nebo, pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **Compose** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového pracovního postupu definice: [operační kód příkladů dat - Compose](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační událost a několik **inicializovat proměnnou** akce. 
   Tyto akce jsou nastavené pro vytváření dva řetězce proměnné a proměnná typu integer. Pokud později otestovat svou aplikaci logiky, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. V aplikaci logiky, ve kterém chcete vytvořit výstup proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "vytváření". Ze seznamu akcí vyberte tuto akci: **Compose**

   ![Výběr akce "Vytvořit"](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. V **vstupy** zadejte vstupy, které chcete použít pro vytváření výstupu. 

   Například, když kliknete do **vstupy** pole v seznamu dynamického obsahu se zobrazí, takže můžete vybrat dřív vytvořenou proměnné:

   ![Vyberte vstupy sestavení](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Tady je příklad dokončení **Compose** akce: 

   ![Dokončení akce "Vytvořit"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [akce psaní](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **Compose** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **Compose** akce.

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **Compose** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když v seznamu dynamického obsahu se otevře, v části **Compose** akci, vyberte **výstup**. 

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a zahrnuje **výstup** polí v předmětu a textu v e-mailu:

   !["Output" polí v akci "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s výsledky akce "Vytvořit"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Akce vytvoření tabulky CSV

Chcete-li vytvořit tabulku hodnot oddělených čárkami (CSV) obsahující vlastnosti a hodnoty z objektu jazyka JavaScript Notation (JSON) objektů v poli, použijte **operace s daty – vytvoření tabulky CSV** akce. Pak můžete použít výslednou tabulku do akce, které následují **vytvoření tabulky CSV** akce. 

Pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **vytvoření tabulky CSV** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového pracovního postupu definice: [operační kód příkladů dat – vytvoření tabulky CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační události a **inicializovat proměnnou** akce. 
   Tato akce je nastavený pro vytvoření proměnné, jejichž počáteční hodnota je pole, která má některé vlastnosti a hodnoty ve formátu JSON. 
   Pokud později otestovat svou aplikaci logiky, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. V aplikaci logiky, ve kterém chcete vytvořit tabulku CSV proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte "Vytvoření tabulky csv" jako filtr. Ze seznamu akcí vyberte tuto akci: **vytvoření tabulky CSV**

   ![Vyberte "sdíleného svazku clusteru vytvořit tabulku" akce](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. V **z** zadejte pole nebo výraz, které chcete použít pro vytvoření tabulky. 

   Například, když kliknete do **z** pole v seznamu dynamického obsahu se zobrazí, takže můžete vybrat dřív vytvořené proměnné:

   ![Vyberte pole výstup pro vytvoření tabulky CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Tady je příklad dokončení **vytvoření tabulky CSV** akce: 

   ![Bylo dokončeno "sdíleného svazku clusteru vytvořit tabulku" akce](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Ve výchozím nastavení tato akce automaticky vytvoří sloupce podle pole položky. 
   Chcete-li ručně vytvořit záhlaví sloupců a hodnoty, zvolte **zobrazit pokročilé možnosti**. 
   Chcete-li zadat jenom vlastní hodnoty, změňte **sloupce** k **vlastní**. 
   Pokud chcete poskytnout vlastní sloupec záhlaví příliš, změňte **zahrnout záhlaví** k **Ano**. 

   > [!TIP]
   > Vytvořit uživatelsky přívětivé tokeny pro vlastnosti v objektech JSON, tak tyto vlastnosti můžete vybrat jako vstupy, použijte [Parsovat JSON](#parse-json-action) před voláním **vytvoření tabulky CSV** akce.

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [akce tabulky](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **vytvoření tabulky CSV** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **vytvoření tabulky CSV** akce.

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **vytvoření tabulky CSV** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když v seznamu dynamického obsahu se otevře, v části **vytvoření tabulky CSV** akci, vyberte **výstup**. 

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a zahrnuje **výstup** pole e-mailu:

   !["Output" polí v akci "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s "sdíleného svazku clusteru vytvořit tabulku" výsledky akcí](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Vytvoření akce tabulky HTML

K vytvoření tabulky HTML, který obsahuje vlastnosti a hodnoty z objektu jazyka JavaScript Notation (JSON) objektů v poli, použijte **operace s daty – vytvoření tabulky HTML** akce. Pak můžete použít výslednou tabulku do akce, které následují **vytvoření tabulky HTML** akce.

Pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **vytvoření tabulky HTML** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového pracovního postupu definice: [operační kód příkladů dat – vytvoření tabulky HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační události a **inicializovat proměnnou** akce. 
   Tato akce je nastavený pro vytvoření proměnné, jejichž počáteční hodnota je pole, která má některé vlastnosti a hodnoty ve formátu JSON. 
   Pokud později otestovat svou aplikaci logiky, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. V aplikaci logiky, ve kterém chcete vytvořit tabulku HTML proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte "Vytvoření tabulky html" jako filtr. Ze seznamu akcí vyberte tuto akci: **vytvoření tabulky HTML**

   ![Výběr akce "Vytvořit HTML tabulku"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. V **z** zadejte pole nebo výraz, které chcete použít pro vytvoření tabulky. 

   Například, když kliknete do **z** pole v seznamu dynamického obsahu se zobrazí, takže můžete vybrat dřív vytvořené proměnné:

   ![Vyberte pole výstup pro vytvoření tabulky HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Tady je příklad dokončení **vytvoření tabulky HTML** akce: 

   ![Dokončení akce "Vytvořit HTML tabulky"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Ve výchozím nastavení tato akce automaticky vytvoří sloupce podle pole položky. 
   Chcete-li ručně vytvořit záhlaví sloupců a hodnoty, zvolte **zobrazit pokročilé možnosti**. 
   Chcete-li zadat jenom vlastní hodnoty, změňte **sloupce** k **vlastní**. 
   Pokud chcete poskytnout vlastní sloupec záhlaví příliš, změňte **zahrnout záhlaví** k **Ano**. 

   > [!TIP]
   > Vytvořit uživatelsky přívětivé tokeny pro vlastnosti v objektech JSON, tak tyto vlastnosti můžete vybrat jako vstupy, použijte [Parsovat JSON](#parse-json-action) před voláním **vytvoření tabulky HTML** akce.

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [akce tabulky](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **vytvoření tabulky HTML** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **vytvoření tabulky HTML** akce.

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **vytvoření tabulky HTML** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když v seznamu dynamického obsahu se otevře, v části **vytvoření tabulky HTML** akci, vyberte **výstup**. 

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a zahrnuje **výstup** pole e-mailu:

   !["Output" polí v akci "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > Při zahrnutí tabulky HTML výstup v akci e-mailu, ujistěte se, že jste nastavili **je HTML** vlastnost **Ano** e-mailu je akce pokročilé možnosti. Tímto způsobem e-mailové akce správně formátuje tabulku HTML.

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s výsledky akce "Vytvořit HTML tabulku"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Akce filtrování pole

Chcete-li vytvořit pole s menší obsahující položky, které splňují určitá kritéria, z existujícího pole, použijte **operace s daty – filtrování pole** akce. Filtrované pole pak můžete použít v akce, které následují po **filtrování pole** akce. 

> [!NOTE]
> Text filtru, který použijete v vaše podmínka je velká a malá písmena. Tuto akci nejde také změnit formát nebo součástí položky v poli. 
> 
> Pro akce, které používají výstup pole **filtrování pole** akce, tyto akce, musíte přijmout pole jako vstup, nebo může být nutné transformovat pole výstup do jiného formátu kompatibilní. 

Pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **filtrování pole** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového pracovního postupu definice: [operační kód příkladů dat – filtrování pole](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační události a **inicializovat proměnnou** akce. 
   Tato akce je nastavený pro vytvoření proměnné, jejichž počáteční hodnota je pole, která má některé ukázkové celých čísel. Pokud později otestovat svou aplikaci logiky, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   > [!NOTE]
   > Přestože tento příklad používá jednoduché celočíselné pole, tato akce je zvláště užitečná pro objekt JSON, pole, kde si můžete vyfiltrovat v závislosti na vlastnosti a hodnoty objektů.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. V aplikaci logiky, ve kterém chcete vytvořit filtrované pole proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "filtrování pole". Ze seznamu akcí vyberte tuto akci: **filtrování pole**

   ![Vyberte akci "Filtrování pole"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. V **z** zadejte pole nebo výraz, který chcete filtrovat. 

   Například, když kliknete do **z** pole v seznamu dynamického obsahu se zobrazí, takže můžete vybrat dřív vytvořené proměnné:

   ![Vyberte pole výstup pro vytvoření filtrovaného pole](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Podmínky zadejte položky pole, které chcete porovnat, vyberte operátor porovnání a určete hodnotu porovnání.

   V tomto příkladu **item()** funkce pro každou položku v poli při přístupu k **filtrování pole** akce hledání položky pole, jehož hodnota je větší než 1:
   
   ![Dokončení akce "Filtrování pole"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [akce dotazu](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **filtrování pole** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **filtrování pole** akce.

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **filtrování pole** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když se otevře seznam dynamického obsahu vyberte **výraz**. K získání výstupu pole z **filtrování pole** akce, zadejte tento výraz, který zahrnuje **filtrování pole** název akce:

   ```
   @actionBody('Filter_array')
   ```

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a obsahuje výstupy z **actionBody('Filter_array')** výrazem v těle v e-mailu:

   ![Akce výstupy akce "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s výsledky akce "Filtrování pole"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Připojte se k akci

Chcete-li vytvořit řetězec, který obsahuje všechny položky z pole a tyto položky oddělujte konkrétní oddělovací znak, použijte **operace s daty – spojení** akce. Potom můžete řetězec v akce, které následují po **připojení** akce.

Pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **připojení** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového definice pracovního postupu: [ Příklady kódu operace dat – připojení](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační události a **inicializovat proměnnou** akce. 
   Tato akce je nastavený pro vytvoření proměnné, jejichž počáteční hodnota je pole, která má některé ukázkové celých čísel. 
   Při testování aplikace logiky později, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. V aplikaci logiky, ve kterém chcete vytvořit řetězec z pole proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "join". Ze seznamu akcí vyberte tuto akci: **Join**

   ![Vyberte akci "Data operace – spojení"](./media/logic-apps-perform-data-operations/select-join-action.png)

4. V **z** zadejte pole, které obsahuje položky, které chcete připojit jako řetězec. 

   Například, když kliknete do **z** pole, seznamu dynamického obsahu, který se zobrazí, takže můžete vybrat dřív vytvořené proměnné:  

   ![Vyberte pole výstup pro vytvoření řetězce](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. V **Seznamte se s** zadejte znak, který chcete použít pro dělicí každou položku pole. 

   Tento příklad používá jako oddělovač dvojtečkou (:).

   ![Zadat oddělovací znak](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [připojte se k akci](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **připojení** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **připojte se k** akce. 

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **připojení** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když v seznamu dynamického obsahu se otevře, v části **připojení** akci, vyberte **výstup**. 

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a zahrnuje **výstup** pole e-mailu:

   !["Output" polí v akci "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s výsledky akce "Join"](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Parsovat JSON akce

K odkazu nebo přístup k vlastnostem v zápisu JSON (JavaScript Object) obsah, uživatelsky přívětivé pole nebo tokenů pro tyto vlastnosti můžete vytvořit pomocí **operace s daty – Parsovat JSON** akce.
Tímto způsobem můžete vybrat tyto vlastnosti ze seznamu dynamického obsahu při zadávání vstupy pro vaši aplikaci logiky. Tato akce zadejte schéma JSON nebo generování schématu JSON z ukázkový obsah JSON nebo datové části.

Pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **Parsovat JSON** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového definice pracovního postupu : [Operační kód příkladů dat – Parsovat JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační události a **inicializovat proměnnou** akce. 
   Tato akce je nastavený pro vytvoření proměnné, jehož počáteční hodnota je objekt JSON, který obsahuje vlastnosti a hodnoty. 
   Pokud později otestovat svou aplikaci logiky, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. V aplikaci logiky, ve které chcete analyzovat obsah JSON proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte "parsování formátu json" jako filtr. Ze seznamu akcí vyberte tuto akci: **Parsovat JSON**

   ![Vyberte akci "Parsovat JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. V **obsah** zadejte obsah JSON, který chcete analyzovat. 

   Například, když kliknete do **obsah** pole v seznamu dynamického obsahu se zobrazí, takže můžete vybrat dřív vytvořené proměnné:

   ![Vyberte objekt JSON pro akce parsovat JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Zadejte schéma JSON, který popisuje obsah JSON, který se analýza. 

   V tomto příkladu je tady schéma JSON:

   ![Zadejte schéma JSON pro objekt ve formátu JSON, který chcete analyzovat](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Pokud nemáte k dispozici schéma, můžete vygenerovat toto schéma z obsahu JSON nebo *datové části*, při analýze. 
   
   1. V **Parsovat JSON** akci, vyberte **k vygenerování schématu použít ukázkovou datovou část**.

   2. V části **zadejte nebo vložte ukázkovou datovou část JSON**, zadejte obsah JSON a pak zvolte **provádí**.

      ![Zadejte obsah JSON pro generování schématu](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [akce Parsovat JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **Parsovat JSON** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **Parsovat JSON** akce.

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **Parsovat JSON** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když v seznamu dynamického obsahu se otevře, v části **Parsovat JSON** akce, teď můžete vybrat vlastnosti z analyzovaného obsah JSON.

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a zahrnuje **FirstName**, **LastName**, a **e-mailu** pole v e-mailu:

   ![Vlastností JSON použitých v akci "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Tady je dokončené e-mailové akce:

   ![Dokončení e-mailové akce](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s výsledky akce "Join"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Vyberte akci

Chcete-li vytvořit pole, která je vytvořená z hodnoty existujícího pole objektů JSON, použijte **operace s daty – výběr** akce. Můžete například vytvořit objekt JSON pro každou hodnotu v celočíselné pole tak, že zadáte vlastnosti, které musí mít každý objekt JSON a způsob namapování hodnoty v poli zdroje k těmto vlastnostem. A přestože můžete změnit součástí těchto objektů JSON, výstup pole má vždy stejný počet položek jako zdrojové pole.

> [!NOTE]
> Pro akce, které používají výstup pole **vyberte** akce, tyto akce, musíte přijmout pole jako vstup, nebo může být nutné transformovat pole výstup do jiného formátu kompatibilní. 

Pokud dáváte přednost práci v editoru kódu zobrazit, můžete zkopírovat v příkladu **vyberte** a **inicializovat proměnnou** definice akce v tomto článku do aplikace logiky podkladového definice pracovního postupu: [Operační kód příkladů dat – vyberte](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. 

   Tento příklad používá na webu Azure portal a aplikaci logiky s **opakování** aktivační události a **inicializovat proměnnou** akce. 
   Tato akce je nastavený pro vytvoření proměnné, jejichž počáteční hodnota je pole, která má některé ukázkové celých čísel. 
   Pokud později otestovat svou aplikaci logiky, můžete ručně spustit vaši aplikaci bez čekání na aktivační událost, která se aktivuje.

   ![Počáteční ukázková aplikace logiky](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. V aplikaci logiky, ve kterém chcete vytvořit pole proveďte jeden z následujících kroků: 

   * Chcete-li přidat akci v posledním kroku, zvolte **nový krok** > **přidat akci**.

     ![Přidání akce](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Přidání akce mezi kroky, najeďte myší na připojení šipku, zobrazí se na symbol plus (+). 
   Vyberte znaménko plus a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "Vyberte". Ze seznamu akcí vyberte tuto akci: **vyberte**

   ![Vyberte akci "Vyberte"](./media/logic-apps-perform-data-operations/select-select-action.png)

4. V **z** zadejte má zdrojové pole.

   Například, když kliknete do **z** pole v seznamu dynamického obsahu se zobrazí, takže můžete vybrat dřív vytvořené proměnné:

   ![Vyberte zdrojové pole pro akce výběru](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. V **mapy** levém sloupci pole, zadejte název vlastnosti, kterou chcete přiřadit všechny hodnoty ve zdrojové pole. V pravém sloupci zadejte výraz, který představuje hodnotu, kterou chcete přiřadit vlastnosti.

   Tento příklad určuje "Product_ID" jako název vlastnosti přiřazení každou hodnotu v poli celého čísla pomocí **item()** funkce ve výrazu, který přistupuje k každou položku pole. 

   ![Zadejte vlastnost objektu JSON a hodnoty pro pole, které chcete vytvořit](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Tady je dokončené akce:

   ![Dokončení akce výběru](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Další informace o této akci v základní definici pracovního postupu najdete v tématu [vyberte akci](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

Potvrďte, zda **vyberte** akce vytvoří očekávané výsledky, poslat oznámení, který obsahuje výstup z **vyberte** akce.

1. V aplikaci logiky, přidejte akci, která vám může poslat výsledky z **vyberte** akce.

2. V akci klikněte kamkoli chcete zobrazit výsledky. Když se otevře seznam dynamického obsahu vyberte **výraz**. K získání výstupu pole z **vyberte** akce, zadejte tento výraz, který zahrnuje **vyberte** název akce:

   ```
   @actionBody('Select')
   ```

   V tomto příkladu **Office 365 Outlook – odeslat e-mail** akce a obsahuje výstupy z **actionBody('Select')** výrazem v těle v e-mailu:

   ![Akce výstupy akce "Odeslat e-mail"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. Teď svou aplikaci logiky spustit ručně. Na panelu nástrojů návrháře zvolte **spustit**. 

   Podle e-mailový konektor, který jste použili, tady jsou výsledky, které získáte:

   ![E-mailu s výsledky akce "Vyberte"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [konektory Logic Apps](../connectors/apis-list.md)
