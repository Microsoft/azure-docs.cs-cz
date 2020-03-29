---
title: Moderování textu s vlastními seznamy termínů – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro správu seznamů můžete vytvářet vlastní seznamy termínů, které se mají používat s rozhraním API pro moderování textu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 16cfb6c15a4d17ff3fb4f7f41f59f9f80af1e9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75382119"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderovat s vlastními seznamy termínů v konzole rozhraní API

Výchozí globální seznam výrazů v Azure Content Moderatoru je dostačující pro většinu potřeb z hlediska moderování obsahu. Možná ale budete potřebovat vyhledat výrazy, které jsou specifické pro vaši organizaci. Například můžete chtít označit názvy konkurentů k další kontrole. 

Pomocí [rozhraní API pro správu seznamů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) můžete vytvářet vlastní seznamy termínů, které se mají používat s rozhraním API pro moderování textu. Operace **Text – obrazovka** prohledá text z důvodu vulgárních výrazů a také porovná text s vlastními a sdílenými seznamy blokování.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Rozhraní API pro správu seznamů můžete použít k následujícím úkolům:
- Vytvoření seznamu
- Přidání výrazů do seznamu
- Porovnání výrazů s výrazy v seznamu
- Odstranění výrazů ze seznamu
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

## <a name="use-the-api-console"></a>Použití konzoly rozhraní API

Než budete moci testovací disk API v online konzole, budete potřebovat klíč předplatného. Tento klíč je umístěn na kartě **Nastavení** v poli **Ocp-Apim-Subscription-Key.** Další informace najdete v tématu [Přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizovat index vyhledávání

Po provádění změn v seznamu termínů je nutné aktualizovat jeho index, aby změny byly zahrnuty do budoucích prohledávek. Tento krok je podobný tomu, jak vyhledávač na ploše (pokud je povolen) nebo webový vyhledávač neustále aktualizuje svůj index, aby zahrnoval nové soubory nebo stránky.

1. V [odkazu rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)vyberte v levé nabídce **seznamy termínů**a pak vyberte **Aktualizovat index hledání**. 

   Otevře se stránka **Seznamy termínů – Aktualizovat index vyhledávání.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Seznamy termínů – výběr oblasti stránky Aktualizovat index hledání](images/test-drive-region.png)

   Otevře **se konzola Seznamy termínů – aktualizovat** konzolu rozhraní API indexu vyhledávání.

3. Do pole **listId** zadejte ID seznamu. Zadejte klíč předplatného a pak vyberte **Odeslat**.

   ![Termín seznamy API - aktualizovat index hledání konzoly odpověď pole](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Vytvoření seznamu výrazů
1. Přejděte na [odkaz rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Otevře **se stránka Seznamy termínů** – Vytvořit stránku.

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Seznamy termínů – vytvoření výběru oblasti stránky](images/test-drive-region.png)

   Seznamy **termínů –** otevře se vytvoření konzoly rozhraní API.
 
3. Do pole **Ocp-Apim-Subscription-Key** zadejte klíč předplatného.

4. Do pole **Text požadavku** zadejte hodnoty pro **Název** (například MyList) a **Description**.

   ![Seznamy termínů – vytvoření názvu a popisu těla požadavku konzoly](images/try-terms-list-create-1.png)

5. Pomocí zástupných symbolů pro párování klíč-hodnota přiřaďte do seznamu popisnější metadata.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Přidejte metadata seznamu jako dvojice klíč-hodnota a ne skutečné termíny.
 
6. Vyberte **Poslat**. Váš seznam je vytvořen. Poznamenejte si hodnotu **ID,** která je přidružena k novému seznamu. Toto ID potřebujete pro další funkce správy seznamu termínů.

   ![Seznamy termínů – pole Vytvořit obsah odpovědi konzoly zobrazuje ID seznamu.](images/try-terms-list-create-2.png)
 
7. Přidejte termíny do seznamu MyList. V levé nabídce vyberte v části **Termín** **položku Přidat termín**. 

   Otevře se stránka **Termín - Přidat termín.** 

8. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Termín - Přidat výběr oblasti stránky Termín](images/test-drive-region.png)

   **Termín - Přidat konzolu rozhraní** API termínu otevře.
 
9. Do pole **listId** zadejte ID seznamu, který jste vygenerovali, a vyberte hodnotu **jazyka**. Zadejte klíč předplatného a pak vyberte **Odeslat**.

   ![Termín - Přidat parametry dotazu konzoly termínu](images/try-terms-list-create-3.png)
 
10. Chcete-li ověřit, zda byl termín přidán do seznamu, vyberte v levé nabídce **možnost Termín**a pak vyberte **možnost Získat všechny podmínky**. 

    **Termín - získat všechny termíny** rozhraní API konzoly otevře.

11. Do pole **listId** zadejte ID seznamu a zadejte klíč předplatného. Vyberte **Poslat**.

12. V poli **Obsah odpovědi** ověřte zadaná výraz.

    ![Termín – v poli Získat obsah odpovědi na konzolu Všechny podmínky jsou uvedeny termíny, které jste zadali.](images/try-terms-list-create-4.png)
 
13. Přidejte několik dalších podmínek. Nyní, když jste vytvořili vlastní seznam termínů, zkuste [skenovat nějaký text](try-text-api.md) pomocí vlastního seznamu termínů. 

## <a name="delete-terms-and-lists"></a>Odstranění výrazů a seznamů

Odstranění výrazu nebo seznamu je jednoduché. Pomocí rozhraní API můžete provést následující úkoly:

- Odstranění výrazu. (**Termín - Odstranit**)
- Odstranění všech výrazů v seznamu bez odstranění seznamu. (**Termín - Odstranit všechny podmínky**)
- Odstranění seznamu a veškerého jeho obsahu. (**Termín Seznamy - Odstranit**)

Tento příklad odstraní jeden termín.

1. V [odkazu rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)vyberte v levé nabídce **možnost Termín**a pak vyberte **Odstranit**. 

   Otevře **se termín - odstranit.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Termín – odstranit výběr oblasti stránky](images/test-drive-region.png)

   Otevře **se konzola Termín - odstranění** rozhraní API.
  
3. Do pole **listId** zadejte ID seznamu, ze kterého chcete termín odstranit. Toto ID je číslo (v našem příkladu **122),** které je vráceno v **seznamech termínů - Získat podrobnosti konzoly** pro MyList. Zadejte termín a vyberte jazyk.
 
   ![Termín – odstranit parametry dotazu konzoly](images/try-terms-list-delete-1.png)

4. Zadejte klíč předplatného a pak vyberte **Odeslat**.

5. Chcete-li ověřit, zda byl termín odstraněn, použijte konzolu **Seznamy termínů – získat vše.**

   ![Termín seznamy - Získat všechny konzole Odpověď obsah pole ukazuje, že termín je odstraněn](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Změna informací o seznamu

Můžete upravit název a popis seznamu a přidat položky metadat.

1. V [odkazu rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)vyberte v levé nabídce **seznamy termínů**a pak vyberte **Aktualizovat podrobnosti**. 

   Otevře se stránka **Seznamy termínů – Aktualizovat podrobnosti.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Seznamy termínů – výběr oblasti stránky Podrobnosti o aktualizaci](images/test-drive-region.png)

   Otevře **se konzola Termín Seznamy - Aktualizace podrobností** rozhraní API.

3. Do pole **listId** zadejte ID seznamu a zadejte klíč předplatného.

4. V poli **Požádat** o tělo proveďte úpravy a pak vyberte **Odeslat**.

   ![Seznamy termínů – úpravy textu aktualizace podrobností o textu](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další kroky

Pomocí rozhraní REST API v kódu nebo začněte s [termínem seznamy .NET rychlý start](term-lists-quickstart-dotnet.md) integrovat s vaší aplikací.
