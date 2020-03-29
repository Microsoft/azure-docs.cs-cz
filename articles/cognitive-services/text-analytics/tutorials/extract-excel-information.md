---
title: Extrahovat informace v Excelu pomocí analýzy textu a Power Automate
titleSuffix: Azure Cognitive Services
description: Naučte se extrahovat excelový text, aniž byste museli psát kód, pomocí analýzy textu a power automatu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201185"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrahovat informace v Excelu pomocí analýzy textu a Power Automate 

V tomto kurzu vytvoříte tok Power Automate pro extrahování textu v tabulce aplikace Excel bez nutnosti psát kód. 

Tento tok bude trvat tabulku problémů hlášených o bytový komplex, a klasifikovat je do dvou kategorií: instalatérské a další. Bude také extrahovat jména a telefonní čísla nájemníků, kteří je poslali. Nakonec tok připojí tyto informace k listu aplikace Excel. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření toku pomocí power automatu
> * Nahrání excelových dat z OneDrivu pro firmy
> * Extrahování textu z Excelu a jeho odeslání do rozhraní API pro analýzu textu 
> * Informace z rozhraní API slouží k aktualizaci listu aplikace Excel.

## <a name="prerequisites"></a>Požadavky

- Účet Microsoft Azure. [Začněte s bezplatnou zkušební verzí](https://azure.microsoft.com/free/) nebo se [přihlaste](https://portal.azure.com/).
- Prostředek analýzy textu. Pokud ho nemáte, můžete [ho vytvořit na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) a k dokončení tohoto kurzu použít bezplatnou vrstvu.
- [Klíč a koncový bod,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) který byl vygenerován pro vás během registrace.
- Tabulka obsahující problémy s tenantem. Příklady dat jsou k dispozici na GitHubu
- Office 365 s OneDrivem pro firmy.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Přidání excelového souboru na OneDrive pro firmy

Stáhněte si ukázkový soubor Excelu z [GitHubu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Tento soubor musí být uložený v účtu OneDrivu pro firmy.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Příklady ze souboru aplikace Excel.":::

Problémy jsou hlášeny v nezpracovaném textu. K extrahování jména a telefonního čísla osoby použijeme rozpoznávání pojmenovaných entit rozhraní API pro analýzu textu. Pak tok bude hledat slovo "instalatérství" v popisu kategorizovat problémy. 

## <a name="create-a-new-power-automate-workflow"></a>Vytvoření nového pracovního postupu Power Automate

Přejděte na [web Power Automate](https://preview.flow.microsoft.com/)a přihlaste se. Potom klepněte na **tlačítko Vytvořit** a **naplánovaný tok**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Obrazovka vytvoření toku.":::


Na stránce **Sestavení naplánovaného toku** inicializujte tok pomocí následujících polí:

|Pole |Hodnota  |
|---------|---------|
|**Název toku**     | **Plánovaná recenze** nebo jiný název.         |
|**Spuštění**     |  Zadejte aktuální datum a čas.       |
|**Opakujte každý**     | **1 hodina**        |

## <a name="add-variables-to-the-flow"></a>Přidání proměnných do toku

> [!NOTE]
> Pokud chcete vidět obrázek dokončeného toku, můžete si ho stáhnout z [GitHubu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Vytvořte proměnné představující informace, které budou přidány do souboru aplikace Excel. Klepněte na **tlačítko Nový krok** a **vyhledejte proměnnou Inicializovat**. Proveďte to čtyřikrát, chcete-li vytvořit čtyři proměnné.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicializovat proměnné.":::

Do proměnných, které jste vytvořili, přidejte následující informace. Představují sloupce souboru aplikace Excel. Pokud jsou některé proměnné sbaleny, můžete na ně kliknout a rozbalit je.

| Akce |Name (Název)   | Typ | Hodnota |
|---------|---------|---|---|
| Inicializovat proměnnou | var_person | Řetězec | Person (Osoba) |
| Inicializovat proměnnou 2 | var_phone | Řetězec | Phone_Number |
| Inicializovat proměnnou 3 | var_plumbing | Řetězec | Instalatérské |
| Inicializovat proměnnou 4 | var_other | Řetězec | ostatní | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informace obsažené v proměnných toku":::

## <a name="read-the-excel-file"></a>Čtení souboru aplikace Excel

Klikněte na **Nový krok** a zadejte **Excel**a ze seznamu akcí vyberte Seznam řádků **v tabulce.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="přidejte řádky aplikace Excel.":::

Přidejte soubor aplikace Excel do toku vyplněním polí v této akci. Tento kurz vyžaduje, aby byl soubor nahrán na OneDrive pro firmy.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="přidejte řádky aplikace Excel.":::

Klikněte na **Nový krok** a přidejte pro každou akci **použít.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="přidejte příkaz použít.":::

Klikněte na **Vybrat výstup z předchozího kroku**. V poli Dynamický obsah, které se zobrazí, vyberte **hodnotu**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Vyberte výstup ze souboru aplikace Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Odeslání požadavku do rozhraní API pro analýzu textu

Pokud jste to ještě neudělali, musíte na webu Azure Portal vytvořit [prostředek analýzy textu.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)

### <a name="create-a-text-analytics-connection"></a>Vytvoření připojení analýzy textu

V části **Použít pro každou**z nich klepněte na tlačítko Přidat **akci**. Přejděte na stránku **klíče a koncového bodu** svého prostředku analýzy textu na webu Azure Portal a získejte klíč a koncový bod pro váš prostředek analýzy textu.

Do toku zadejte následující informace a vytvořte nové připojení Textové analýzy.

> [!NOTE]
> Pokud jste již vytvořili připojení Analýzy textu a chcete změnit podrobnosti o připojení, klikněte na tři tečky v pravém horním rohu a klikněte na **+ Přidat nové připojení**.

| Pole           | Hodnota                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Název připojení | Název připojení k prostředku analýzy textu. Například, `TAforPowerAutomate`. |
| Klíč účtu     | Klíč pro váš prostředek analýzy textu.                                                                                   |
| Adresa URL webu        | Koncový bod pro váš prostředek analýzy textu.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

## <a name="extract-the-excel-content"></a>Extrahování obsahu aplikace Excel 

Po vytvoření připojení vyhledejte **analýzu textu** a vyberte **entity**. Tím se zextrahují informace ze sloupce popisu problému.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

Klepněte do **textového** pole a z oken dynamického obsahu, která se zobrazí, vyberte **Popis.** Zadejte `en` jazyk. (Pokud nevidíte jazyk, klikněte na Zobrazit rozšířené možnosti.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::


## <a name="extract-the-person-name"></a>Extrahovat jméno osoby

Dále najdeme typ entity osoby ve výstupu Text Analytics. V rámci položky **Použít pro každou z nich**klepněte na tlačítko Přidat **akci**a vytvořte další **položku Použít pro každou** akci. Klikněte do textového pole a v okně Dynamický obsah, které se zobrazí, vyberte **Entity.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

V rámci nově vytvořené **akce Použít pro každou akci 2** klikněte na Přidat **akci**a přidejte ovládací prvek **Podmínka.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

V okně Podmínka klikněte na první textové pole. V okně Dynamický obsah **vyhledejte typ entity** a vyberte ho.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

Ujistěte se, že druhý rámeček je nastaven na **.** Pak vyberte třetí pole `var_person` a vyhledejte v okně Dynamický obsah. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

Do podmínky **Pokud ano** zadejte do aplikace Excel a pak vyberte **Aktualizovat řádek**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

Zadejte informace o Excelu a aktualizujte pole **Klíčový sloupec**, **Hodnota klíče** a **Název osoby.** Tím se připojí název zjištěný rozhraním API k listu aplikace Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

## <a name="get-the-phone-number"></a>Získejte telefonní číslo

Minimalizujte **akci Použít pro každou akci 2** kliknutím na název. Pak přidejte další **použít pro každou** akci, stejně jako předtím. bude **pojmenováno Platí pro každý 3**. Vyberte textové pole a přidejte **entity** jako výstup pro tuto akci. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

V rámci **použít pro každý 3**, přidejte condition ovládací prvek. **Condition** Bude **pojmenována Podmínka 2**. V prvním textovém poli vyhledejte a přidejte **typ entity** z okna Dynamický obsah. Ujistěte se, že středový rámeček je nastaven **na .** Potom do pravého textového `var_phone`pole zadejte . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

V podmínce **Pokud ano** přidejte akci **Aktualizovat řádek.** Poté zadejte informace, jako jsme to udělali výše, pro sloupec telefonních čísel listu aplikace Excel. Tím připojíte telefonní číslo zjištěné rozhraním API k listu aplikace Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::


## <a name="get-the-plumbing-issues"></a>Získejte instalatérské problémy

Minimalizovat **Použít pro každé 3** kliknutím na název. Pak vytvořte další **Použít pro každou** v nadřazené akci. Vyberte textové pole a přidejte **entity** jako výstup pro tuto akci z okna Dynamický obsah. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::


Dále tok zkontroluje, zda popis problému z řádku tabulky aplikace Excel obsahuje slovo "instalatérství". Pokud ano, přidá "instalatérské" ve sloupci IssueType. Pokud ne, zadáme "jiné".

Uvnitř **akce Použít pro každou 4** akce přidejte ovládací prvek **stavu.** Bude **pojmenována podmínka 3**. V prvním textovém poli vyhledejte a přidejte **popis** ze souboru aplikace Excel pomocí okna Dynamický obsah. Ujistěte se, že středové pole **obsahuje**. Potom v pravém textovém poli `var_plumbing`vyhledejte a vyberte . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::


V podmínce **Pokud ano** klepněte na tlačítko **Přidat akci**a vyberte aktualizovat **řádek**. Pak zadejte informace jako předtím. Ve sloupci IssueType `var_plumbing`vyberte . To bude platit "instalatérské" štítek na řádek.

V **podmínce Pokud žádná** klepněte na tlačítko **Přidat akci**a vyberte aktualizovat **řádek**. Pak zadejte informace jako předtím. Ve sloupci IssueType `var_other`vyberte . To bude platit "jiný" popisek na řádek.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Přidejte do svého toku přihlašovací údaje pro analýzu textu.":::

## <a name="test-the-workflow"></a>Test pracovního postupu

V pravém horním rohu obrazovky klikněte na **Uložit**a potom na **Testovat**. Vyberte **možnost Budu provádět spouštěcí akci**. Klepněte na tlačítko **Uložit & test**, Spustit **tok**a potom **hotovo**.

Excelový soubor se aktualizuje ve vašem účtu OneDrivu. Bude to vypadat jako níže.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Aktualizovaná tabulka aplikace Excel.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte další řešení](../text-analytics-user-scenarios.md)
