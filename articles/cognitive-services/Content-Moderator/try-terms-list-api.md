---
title: Moderování textu se výraz vlastní seznamy – Content Moderator
titlesuffix: Azure Cognitive Services
description: Chcete-li vytvořit vlastní seznamy termínů pro použití s rozhraním API pro moderování textu pomocí rozhraní API pro správu seznamu.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: fea671df0609b9c4dca9eaae99d1bfe667616837
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259423"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Střední se seznamy vlastní termín v konzole pro rozhraní API

Výchozí globální seznam výrazů v Azure Content Moderatoru je dostačující pro většinu potřeb z hlediska moderování obsahu. Možná ale budete potřebovat vyhledat výrazy, které jsou specifické pro vaši organizaci. Například můžete chtít označit názvy konkurentů k další kontrole. 

Použít [rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) vytvořit vlastní seznamy termínů pro použití s rozhraním API pro moderování textu. **Text – obrazovky** operace prohledá textu pro vulgárních výrazů a také porovná text před vlastní a sdílené seznamů zakázaných položek.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Rozhraní API pro správu seznamu můžete provádět následující úlohy:
- Vytvoření seznamu
- Přidání výrazů do seznamu
- Porovnání výrazů s výrazy v seznamu
- Odstranění výrazů ze seznamu
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly

Předtím, než můžete vyzkoušet rozhraní API v konzole online, budete potřebovat klíč předplatného. Tento klíč se nachází na **nastavení** kartě **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [Přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizujte index vyhledávání

Po provedení změn na seznam termínů, je nutné aktualizovat jeho index pro změny, které mají být zahrnuty v budoucích kontrolách. Tento krok je podobný jak vyhledávacího webu na ploše (je-li povoleno) nebo webového vyhledávacího webu průběžně aktualizuje jeho index nových souborů nebo stránky.

1.  V [referenční dokumentace rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), v nabídce vlevo vyberte **obsahuje seznam termínů**a pak vyberte **aktualizovat Index vyhledávání**. 

  **Termín uvádí – aktualizovat Index vyhledávání** otevře se stránka.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Seznamy termínů – výběr oblasti stránek indexu vyhledávání aktualizace](images/test-drive-region.png)

  **Termín uvádí – aktualizovat Index vyhledávání** otevře se konzola rozhraní API.

3.  V **listId** zadejte ID seznamu. Zadejte klíč předplatného a pak vyberte **odeslat**.

  ![Seznamy termínů API – Index vyhledávání aktualizace konzoly pole obsahu odpovědi](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Vytvoření seznamu výrazů
1.  Přejděte [referenční dokumentace rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  **Obsahuje seznam termínů – vytvořit** otevře se stránka.

2.  Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín seznamy – vytvoření stránky oblast výběru](images/test-drive-region.png)

  **Obsahuje seznam termínů – vytvořit** otevře se konzola rozhraní API.
 
3.  V **Ocp-Apim-Subscription-Key** zadejte váš klíč předplatného.

4.  V **text žádosti** , zadejte hodnoty pro pole **název** (například MyList) a **popis**.

  ![Termín seznamy – vytvoření konzoly žádost subjektu název a popis](images/try-terms-list-create-1.png)

5.  Pomocí páru klíč hodnota zástupné symboly můžete přiřadit více popisná metadata do seznamu.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Přidáte seznam metadat jako páry klíč hodnota a ne skutečné podmínky.
 
6.  Vyberte **Poslat**. Vytvoření seznamu. Poznámka: **ID** hodnotu, která je přidružena k nového seznamu. Toto ID budete potřebovat další funkce správy seznam termínů.

  ![Termín seznamy – vytvoření konzoly odpovědi obsahu pole se zobrazí seznam ID](images/try-terms-list-create-2.png)
 
7.  Přidání podmínky do MyList. V nabídce vlevo v části **termín**vyberte **přidat termín**. 

  **Termín – přidejte výraz** otevře se stránka. 

8.  Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín – přidání výběr oblasti stránek termín](images/test-drive-region.png)

  **Termín – přidejte výraz** otevře se konzola rozhraní API.
 
9.  V **listId** zadejte ID seznamu, který jste vygenerovali a vyberte hodnotu pro **jazyka**. Zadejte klíč předplatného a pak vyberte **odeslat**.

  ![Termín – přidat parametry dotazu konzoly termín](images/try-terms-list-create-3.png)
 
10. Chcete-li ověřit, že termín je přidaný do seznamu v nabídce vlevo vyberte **termín**a pak vyberte **získat všechny podmínky**. 

  **Termín - získat všechny podmínky** otevře se konzola rozhraní API.

11. V **listId** zadejte ID seznamu a pak zadejte klíč předplatného. Vyberte **Poslat**.

12. V **obsah odpovědi** zkontrolujte podmínky, které jste zadali,.

  ![Termín - Get všechny podmínky konzoly odpovědi pole obsahu seznamy podmínky, které jste zadali](images/try-terms-list-create-4.png)
 
13. Přidáte pár další podmínky. Teď, když vytvoříte vlastní seznam termínů, zkuste [prohledávání nějaký text](try-text-api.md) pomocí seznamu vlastní termín. 

## <a name="delete-terms-and-lists"></a>Odstranění výrazů a seznamů

Odstranění výrazu nebo seznamu je jednoduché. Rozhraní API můžete provádět následující úlohy:

- Odstranění výrazu. (**Termín – odstranit**)
- Odstranění všech výrazů v seznamu bez odstranění seznamu. (**Termín – odstranit všechny podmínky**)
- Odstranění seznamu a veškerého jeho obsahu. (**Seznamy termínů - Delete**)

Tento příklad odstraní jeden termín.

1.  V [referenční dokumentace rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), v nabídce vlevo vyberte **termín**a pak vyberte **odstranit**. 

  **Termín – odstranit** otevře.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín – výběr oblasti stránek Delete](images/test-drive-region.png)

  **Termín – odstranit** otevře se konzola rozhraní API.
  
3.  V **listId** , zadejte ID seznamu, který chcete odstranit termín, který z pole. Toto ID je číslo (v našem příkladu **122**), který je vrácen ve **termín obsahuje seznam - podrobnosti získání** konzoly, kde MyList. Zadejte výraz a vyberte požadovaný jazyk.
 
  ![Termín – parametry dotazu konzoly Delete](images/try-terms-list-delete-1.png)

4.  Zadejte klíč předplatného a pak vyberte **odeslat**.

5.  Chcete-li ověřit, že byl odstraněn termín, použijte **termín seznam – všechno můžete získat** konzoly.

  ![Termín seznamy – získání obsahu pole ukazuje, že termín je všechny konzoly odpovědi](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Informace o změně seznamu

Můžete upravit název a popis seznamu a přidat položky metadat.

1.  V [referenční dokumentace rozhraní API pro správu seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), v nabídce vlevo vyberte **obsahuje seznam termínů**a pak vyberte **podrobnosti o aktualizacích**. 

  **Výraz obsahuje seznam - podrobnosti o aktualizacích** otevře se stránka.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Seznamy termínů – podrobné informace o aktualizaci stránky oblast výběru](images/test-drive-region.png)

  **Výraz obsahuje seznam - podrobnosti o aktualizacích** otevře se konzola rozhraní API.

3.  V **listId** zadejte ID seznamu a pak zadejte klíč předplatného.

4.  V **text žádosti** udělejte úpravy a potom vyberte **odeslat**.

  ![Seznamy termínů – podrobnosti o aktualizacích konzole úpravy textu požadavku](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [výraz obsahuje rychlý úvod k .NET](term-lists-quickstart-dotnet.md) integrovat s vaší aplikací.
