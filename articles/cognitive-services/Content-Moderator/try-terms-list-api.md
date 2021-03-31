---
title: Střední text s vlastními seznamy termínů – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro správu seznamu můžete vytvořit vlastní seznamy podmínek, které se použijí v rozhraní API pro moderování textu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800036"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Střední s vlastními seznamy podmínek v konzole API

Výchozí globální seznam výrazů v Azure Content Moderatoru je dostačující pro většinu potřeb z hlediska moderování obsahu. Možná ale budete potřebovat vyhledat výrazy, které jsou specifické pro vaši organizaci. Například můžete chtít označit názvy konkurentů k další kontrole. 

Pomocí [rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) můžete vytvořit vlastní seznamy podmínek, které se použijí v rozhraní API pro moderování textu. Operace **text-Screen** vyhledá text na základě vulgárních výrazů a také porovná text s vlastním a sdíleným adres.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Pomocí rozhraní API pro správu seznamu můžete provádět následující úlohy:
- Vytvoření seznamu
- Přidání výrazů do seznamu
- Porovnání výrazů s výrazy v seznamu
- Odstranění výrazů ze seznamu
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

## <a name="use-the-api-console"></a>Použití konzoly API

Než budete moct otestovat rozhraní API v online konzole, budete potřebovat svůj klíč předplatného. Tento klíč je umístěný na kartě **Nastavení** v poli **OCP-APIM-Subscription-Key** . Další informace najdete v tématu [Přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizovat index vyhledávání

Až provedete změny v seznamu termínů, je nutné aktualizovat index, aby se změny projevily v budoucích kontrolách. Tento krok je podobný tomu, jak vyhledávací modul na ploše (Pokud je povolen) nebo webový vyhledávací modul průběžně aktualizuje svůj index, aby zahrnoval nové soubory nebo stránky.

1. V [referenčních informacích k rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)v nabídce vlevo vyberte **seznamy termínů** a pak vyberte **Aktualizovat index vyhledávání**. 

   Otevře se stránka **termín seznamy-obnovit index vyhledávání** .

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Seznam termínů – aktualizovat výběr oblasti stránky indexu hledání](images/test-drive-region.png)

   Otevře se modul **seznam termínů – aktualizuje** se konzola rozhraní API indexu vyhledávání.

3. Do pole **listId** zadejte ID seznamu. Zadejte svůj klíč předplatného a pak vyberte **Odeslat**.

   ![Termín vypisuje rozhraní API – aktualizovat obsah odpovědi konzoly indexu hledání](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Vytvoření seznamu výrazů
1. Přejít na [Reference k rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Otevře se stránka **termíny seznamy – vytvořit** .

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Seznamy podmínek – vytvoření výběru oblasti stránky](images/test-drive-region.png)

   Otevře se **seznam termínů-vytvořit** konzolu rozhraní API.
 
3. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

4. Do pole **Text žádosti** zadejte hodnoty pro **Name** (například myList) a **Description (popis**).

   ![Terms – vytvoření názvu a popisu textu žádosti konzoly](images/try-terms-list-create-1.png)

5. Použijte zástupné symboly dvojice klíč-hodnota k přiřazení dalších popisných metadat k seznamu.

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   Přidejte metadata seznamu jako páry klíč-hodnota, nikoli skutečné výrazy.
 
6. Vyberte **Odeslat**. Vytvoří se Váš seznam. Poznamenejte si hodnotu **ID** , která je přidružená k novému seznamu. Toto ID budete potřebovat pro jiné funkce správy seznamu termínů.

   ![Seznamy termínů – pole vytvořit obsah odpovědi konzoly zobrazuje ID seznamu](images/try-terms-list-create-2.png)
 
7. Přidejte do MyList výrazy. V nabídce vlevo v části **termín** vyberte **Přidat termín**. 

   Otevře se stránka s termínem **Přidat termín** . 

8. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Přidat výběr oblasti termínu stránky](images/test-drive-region.png)

   Otevře se konzola rozhraní API termínu **Přidat** .
 
9. Do pole **listId** zadejte ID seznamu, které jste vygenerovali, a vyberte hodnotu **jazyk**. Zadejte svůj klíč předplatného a pak vyberte **Odeslat**.

   ![Přidat parametry dotazu konzoly pro termín](images/try-terms-list-create-3.png)
 
10. Chcete-li ověřit, zda byl termín přidán do seznamu, vyberte v nabídce vlevo možnost **termín** a pak vyberte možnost **získat všechny podmínky**. 

    Otevře se konzola rozhraní API **Term-get all terms** .

11. Do pole **listId** zadejte ID seznamu a potom zadejte svůj klíč předplatného. Vyberte **Odeslat**.

12. V poli **obsah odpovědi** Ověřte zadané výrazy.

    ![V poli termín – získat všechny podmínky obsah odpovědi konzoly je uveden seznam podmínek, které jste zadali.](images/try-terms-list-create-4.png)
 
13. Přidejte několik dalších podmínek. Teď, když jste vytvořili vlastní seznam podmínek, zkuste [zkontrolovat text](try-text-api.md) pomocí vlastního seznamu termínů. 

## <a name="delete-terms-and-lists"></a>Odstranění výrazů a seznamů

Odstranění výrazu nebo seznamu je jednoduché. Pomocí tohoto rozhraní API provedete následující úlohy:

- Odstranění výrazu. (**Doba odstranění**)
- Odstranění všech výrazů v seznamu bez odstranění seznamu. (**Podmínka – odstranění všech podmínek**)
- Odstranění seznamu a veškerého jeho obsahu. (**Seznamy termínů-odstranit**)

Tento příklad odstraní jeden výraz.

1. V [referenčních informacích k rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)v nabídce vlevo vyberte **termín** a pak vyberte **Odstranit**. 

   Otevře se **výraz – odstranění** .

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Vymažte výběr oblasti stránky s termínem odstranění](images/test-drive-region.png)

   Otevře se konzola rozhraní API **pro odstranění termínů** .
  
3. Do pole **listId** zadejte ID seznamu, ze kterého chcete odstranit termín. Toto ID je číslo (v našem příkladu, **122**), které se vrátí do konzoly **seznam termínů – získat podrobnosti** pro myList. Zadejte termín a vyberte jazyk.
 
   ![Podmínka – odstranění parametrů dotazu konzoly](images/try-terms-list-delete-1.png)

4. Zadejte svůj klíč předplatného a pak vyberte **Odeslat**.

5. Chcete-li ověřit, zda byl termín odstraněn, použijte **seznam termínů – získat veškerou** konzolu.

   ![Seznamy termínů – získat obsah všech odpovědí na konzolu zobrazí, že termín je odstraněný.](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Změnit informace o seznamu

Můžete upravit název a popis seznamu a přidat položky metadat.

1. V [referenčních informacích k rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)v nabídce vlevo vyberte **seznamy termínů** a pak vyberte **aktualizovat podrobnosti**. 

   Otevře se stránka **termín seznamy – podrobnosti aktualizace** .

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Seznamy termínů – výběr oblasti stránky s podrobnostmi aktualizace](images/test-drive-region.png)

   Otevře se konzola **seznam termínů – podrobnosti o aktualizaci** rozhraní API.

3. Do pole **listId** zadejte ID seznamu a potom zadejte svůj klíč předplatného.

4. V poli **Text žádosti** proveďte úpravy a potom vyberte **Odeslat**.

   ![Seznamy termínů – změny v konzole – podrobnosti o aktualizaci textu](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další kroky

Použijte REST API ve svém kódu nebo začněte s [termínem Seznamte se s rychlým](term-lists-quickstart-dotnet.md) startem .NET pro integraci s vaší aplikací.
