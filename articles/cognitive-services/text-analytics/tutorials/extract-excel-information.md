---
title: Extrakce informací v Excelu pomocí Analýza textu a automatizace
titleSuffix: Azure Cognitive Services
description: Naučte se extrahovat text z Excelu bez nutnosti psát kód, a to pomocí Analýza textu a Power automatizuje.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359897"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrakce informací v Excelu pomocí Analýza textu a automatizace 

V tomto kurzu vytvoříte tok automatizovaného automatizace pro extrakci textu v excelové tabulce, aniž byste museli psát kód. 

Tento tok bude pobírat tabulku problémů hlášených komplexním objektem Apartment a klasifikovat je do dvou kategorií: domovní instalace a další. Také si vyextrahuje názvy a telefonní čísla klientů, kteří je odeslali. Tok pak tyto informace přidá do excelového listu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření toku pomocí možnosti automatizace
> * Nahrávání dat Excelu z OneDrivu pro firmy
> * Extrakce textu z aplikace Excel a odeslání do rozhraní API pro analýzu textu 
> * K aktualizaci excelového listu použijte informace z rozhraní API.

## <a name="prerequisites"></a>Předpoklady

- Účet Microsoft Azure. [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/cognitive-services/) nebo se [přihlaste](https://portal.azure.com/).
- Prostředek Analýza textu. Pokud ho ještě nemáte, můžete [ho vytvořit v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) a k dokončení tohoto kurzu použít bezplatnou úroveň.
- [Klíč a koncový bod](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který jste vygenerovali během registrace.
- Tabulka obsahující problémy klienta. Ukázková data jsou k dispozici na GitHubu
- Microsoft 365 s OneDrivem pro firmy.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Přidání excelového souboru do OneDrivu pro firmy

Stáhněte si ukázkový excelový soubor z [GitHubu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Tento soubor musí být uložený ve vašem účtu OneDrivu pro firmy.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Příklady z excelového souboru.":::

Problémy jsou hlášeny v nezpracovaném textu. K extrakci jména osoby a telefonního čísla použijeme rozpoznávání pojmenovaných entit rozhraní API pro analýzu textu. Tok pak bude v popisu Hledat slovo "domovníing", aby se tyto problémy zařadí do kategorií. 

## <a name="create-a-new-power-automate-workflow"></a>Vytvořit nový pracovní postup automatizace napájení

Přejít na [Web Power](https://preview.flow.microsoft.com/)Automata a přihlásit se. Pak klikněte na **vytvořit** a **naplánovaný tok**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Obrazovka vytváření toku.":::


Na stránce **sestavit naplánovaný tok** inicializujte svůj tok pomocí následujících polí:

|Pole |Hodnota  |
|---------|---------|
|**Název toku**     | **Naplánovaná kontrola** nebo jiný název.         |
|**Spouštění**     |  Zadejte aktuální datum a čas.       |
|**Opakovat každých**     | **1 hodina**        |

## <a name="add-variables-to-the-flow"></a>Přidání proměnných do toku

> [!NOTE]
> Pokud chcete zobrazit obrázek dokončeného toku, můžete si ho stáhnout z [GitHubu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Vytvořte proměnné představující informace, které budou přidány do excelového souboru. Klikněte na **Nový krok** a vyhledejte **inicializační proměnnou**. Provedete to čtyřikrát, chcete-li vytvořit čtyři proměnné.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicializovat proměnné.":::

Do proměnných, které jste vytvořili, přidejte následující informace. Představují sloupce excelového souboru. Pokud jsou některé proměnné sbalené, můžete je kliknutím rozbalit.

| Akce |Název   | Typ | Hodnota |
|---------|---------|---|---|
| Inicializovat proměnnou | var_person | Řetězec | Person (Osoba) |
| Inicializovat proměnnou 2 | var_phone | Řetězec | Phone_Number |
| Inicializovat proměnnou 3 | var_plumbing | Řetězec | vložení |
| Inicializovat proměnnou 4 | var_other | Řetězec | other | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informace obsažené v proměnných toku":::

## <a name="read-the-excel-file"></a>Čtení excelového souboru

Klikněte na **Nový krok** a zadejte **Excel** a pak ze seznamu akcí vyberte seznam řádků, které jsou k **dispozici v tabulce** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Přidejte řádky aplikace Excel.":::

Do tohoto toku přidejte excelový soubor vyplněním polí v této akci. Tento kurz vyžaduje odeslání souboru do OneDrivu pro firmy.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="vyplnit řádky v aplikaci Excel":::

Klikněte na **Nový krok** a přidejte **pro každou akci použít** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Přidejte příkaz Apply.":::

Klikněte na **vybrat výstup z předchozího kroku**. V poli dynamický obsah, který se zobrazí, vyberte možnost **hodnota**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Vyberte výstup z excelového souboru.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Odeslat žádost do rozhraní API pro analýzu textu

Pokud jste to ještě neudělali, musíte v Azure Portal vytvořit [prostředek analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) .

### <a name="create-a-text-analytics-connection"></a>Vytvoření Analýza textuho připojení

V části **platí pro** klikněte na **přidat akci**. V Azure Portal přejít na stránku **klíč a koncový bod** prostředku analýza textu a získejte klíč a koncový bod pro prostředek analýza textu.

V toku zadejte následující informace, chcete-li vytvořit nové připojení Analýza textu.

> [!NOTE]
> Pokud jste již vytvořili Analýza textu připojení a chcete změnit podrobnosti o připojení, klikněte na tlačítko se třemi tečkami v pravém horním rohu a klikněte na **+ Přidat nové připojení**.

| Pole           | Hodnota                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Název připojení | Název připojení k vašemu prostředku Analýza textu. Například, `TAforPowerAutomate`. |
| Klíč účtu     | Klíč pro prostředek Analýza textu.                                                                                   |
| Adresa URL webu        | Koncový bod pro prostředek Analýza textu.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu.":::

## <a name="extract-the-excel-content"></a>Extrakce obsahu aplikace Excel 

Po vytvoření připojení vyhledejte **Analýza textu** a vyberte **entity**. Tato akce extrahuje informace ze sloupce Description (popis problému).

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Přidejte Analýza textu entit.":::

Klikněte do **textového** pole a vyberte **Popis** z okna dynamického obsahu, které se zobrazí. Zadejte `en` pro jazyk. (Pokud nevidíte jazyk, klikněte na Zobrazit pokročilé možnosti)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Přidejte nastavení Analýza textu.":::


## <a name="extract-the-person-name"></a>Extrahovat jméno osoby

V dalším kroku budeme ve výstupu Analýza textu najít typ entity Person (osoba). V části **použít u každého** klikněte na **přidat akci** a vytvořte další **použít pro každou** akci. Klikněte do textového pole a v zobrazeném okně dynamického obsahu vyberte **entity** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. odst":::

V nově vytvořené akci **použít u každé 2** akce klikněte na **přidat akci** a přidejte ovládací prvek **podmínky** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 1":::

V okně Podmínka klikněte na první textové pole. V okně dynamický obsah vyhledejte **entity type** a vyberte je.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 4":::

Ujistěte se, že je druhé pole nastaveno na hodnotu **je rovno**. Pak vyberte třetí pole a vyhledejte `var_person` v okně dynamického obsahu. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. čl":::

V podmínce **if Yes (Pokud ano** ) zadejte do Excelu a pak vyberte **aktualizovat řádek**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 6":::

Zadejte informace o Excelu a aktualizujte **klíčový sloupec**, **hodnotu klíče** a pole jméno **osoby** . Tím se do excelového listu připojí název zjištěný rozhraním API. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. čl":::

## <a name="get-the-phone-number"></a>Získat telefonní číslo

Kliknutím na název minimalizujete **každou akci použít na každou 2** . Pak přidejte další **použít pro každou** akci, třeba před. bude se pojmenovat **pro každé 3**. Vyberte textové pole a jako výstup pro tuto akci přidejte **entity** . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 8":::

V rámci **použít na každé 3** přidejte ovládací prvek **podmínky** . Bude se pojmenovat **Podmínka 2**. Do prvního textového pole vyhledejte a přidejte **typ entity** z okna dynamického obsahu. Ujistěte se, že je prostřední pole nastavené na hodnotu **je rovno**. Potom do pravého textového pole zadejte `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 9":::

V podmínce **if Yes (Pokud ano** ) přidejte akci **aktualizovat řádek** . Pak zadejte informace, jako jsme výše, pro sloupec telefonní čísla v excelovém listu. Tím se do excelového listu připojí telefonní číslo rozpoznané rozhraním API. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 10pruhový":::


## <a name="get-the-plumbing-issues"></a>Získání problémů s instalací

Kliknutím na název **můžete minimalizovat použití na každé tři** . Pak vytvořte další **použít pro každé** nadřazené akci. Vyberte textové pole a jako výstup pro tuto akci přidejte **entity** z okna dynamického obsahu. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. odst":::


V dalším kroku bude tok kontrolovat, zda popis problému z řádku excelové tabulky obsahuje slovo "domovníing". Pokud ano, přidá se "instalace" do sloupce IssueType. Pokud ne, zadáme "other".

V rámci akce **použít u každé 4** přidejte ovládací prvek **podmínky** . Bude se pojmenovat **Podmínka 3**. Do prvního textového pole vyhledejte a přidejte **Popis** ze souboru aplikace Excel pomocí okna dynamického obsahu. Ujistěte se, že střední box **obsahuje**. Pak v pravém textovém poli vyhledejte a vyberte `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 12,5":::


V **případě podmínky Ano** klikněte na **přidat akci** a vyberte **aktualizovat řádek**. Pak zadejte informace, jako jsou třeba. Ve sloupci IssueType vyberte `var_plumbing` . Tím se na řádek aplikuje popisek "instalace".

V části **Pokud není** podmínka klikněte na **přidat akci** a vyberte **aktualizovat řádek**. Pak zadejte informace, jako jsou třeba. Ve sloupci IssueType vyberte `var_other` . Tím se na řádek aplikuje popisek "jiné".

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Přidejte do svého toku přihlašovací údaje Analýza textu. 13,5":::

## <a name="test-the-workflow"></a>Test pracovního postupu

V pravém horním rohu obrazovky klikněte na **Uložit** a pak na **test**. Vyberte možnost  **provedem akci aktivace**. Klikněte na **uložit & test**, **Spustit tok** a potom na **Hotovo**.

Excelový soubor se aktualizuje v účtu OneDrive. Bude vypadat podobně jako v následujícím příkladu.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Aktualizovaná Excelová tabulka":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumat další řešení](../text-analytics-user-scenarios.md)
