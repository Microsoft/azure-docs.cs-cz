---
title: Střední textu pomocí vlastní termín seznamy v obsahu moderátora Azure | Microsoft Docs
description: Test-Drive vlastní termín zobrazí v konzole obsahu moderátora rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2542e4590781879408aafe8d072eceef157e02c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342509"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Střední pomocí vlastní termín seznamů v konzole rozhraní API

Pro většinu obsahu přerušování potřeb stačí výchozí globální seznam termínů v moderátora obsahu Azure. Nicméně budete muset obrazovky podmínek, které jsou specifické pro vaši organizaci. Například můžete chtít názvy konkurence značek hodnocení. 

Použít [rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) k vytvoření vlastních seznamů podmínky pro použití s rozhraní API přerušování Text. **Text – obrazovky** operaci vyhledá text testujeme a taky porovnává text proti sdílené a vlastní rozhraní API.

> [!NOTE]
> Maximální limit je **5 termín uvádí** s každou seznamu **není delší než 10 000 podmínky**.
>

Rozhraní API seznamu správy můžete provést následující úkoly:
- Vytvoří seznam.
- Přidáte do seznamu podmínek.
- Obrazovky podmínky s výrazy v seznamu.
- Podmínky odstraňte ze seznamu.
- Odstranění seznamu.
- Upravte informace o seznamu.
- Aktualizujte index, aby se změny provedené v seznamu jsou zahrnuté v nové prohledání.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly

Než můžete test-drive rozhraní API v konzole online, musíte svůj klíč předplatného. Tento klíč se nachází na **nastavení** ve **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizovat index vyhledávání

Když provedete změny v seznamu termín, je nutné aktualizovat její index pro změny, které mají být zahrnuty v budoucích kontrolách. Tento krok je podobný jak vyhledávacího webu na ploše (Pokud je povoleno) nebo vyhledávací web průběžně aktualizuje její index zahrnout nových souborů nebo stránky.

1.  V [referenční dokumentace rozhraní API správy seznamu termín](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), v nabídce vlevo vyberte **uvádí termín**a potom vyberte **aktualizovat Index vyhledávání**. 

  **Termín uvádí - aktualizovat Index vyhledávání** otevře se stránka.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín seznamy – vybrané oblasti indexu vyhledávání aktualizace stránky](images/test-drive-region.png)

  **Termín uvádí - aktualizovat Index vyhledávání** otevře se konzola rozhraní API.

3.  V **listId** zadejte ID seznamu. Zadejte svůj klíč předplatného a potom vyberte **odeslat**.

  ![Termín seznamy API – Index vyhledávání aktualizace konzoly pole obsahu odpovědi](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Vytvoří seznam podmínek
1.  Přejděte na [referenční dokumentace rozhraní API správy seznamu termín](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  **Termín uvádí - vytvořit** otevře se stránka.

2.  Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín seznamy – vytvoření výběr oblast stránky](images/test-drive-region.png)

  **Termín uvádí - vytvořit** otevře se konzola rozhraní API.
 
3.  V **Ocp-Apim-Subscription-Key** zadejte svůj klíč předplatného.

4.  V **text žádosti** zadejte hodnoty pro **název** (například MyList) a **popis**.

  ![Termín seznamy – vytvoření konzoly požadavek textu název a popis](images/try-terms-list-create-1.png)

5.  Použijte zástupné symboly dvojice klíč hodnota přiřadit více popisná metadata do seznamu.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Přidejte seznam metadat jako páry klíč hodnota a ne skutečné podmínky.
 
6.  Vyberte **Poslat**. Vytvoří se váš seznam. Poznámka: **ID** hodnotu, která souvisí s nového seznamu. Musíte toto ID pro další funkce správy seznamu podmínek.

  ![Termín seznamy – vytvoření konzole odpovědi obsahu pole se zobrazí seznam ID](images/try-terms-list-create-2.png)
 
7.  Přidání podmínek do MyList. V levé nabídce v části **termín**, vyberte **přidat termín**. 

  **Termín - přidat termín** otevře se stránka. 

8.  Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín – přidání vybrané oblasti termín stránky](images/test-drive-region.png)

  **Termín - přidat termín** otevře se konzola rozhraní API.
 
9.  V **listId** pole, zadejte ID seznamu, který jste vygenerovali a vyberte hodnotu pro **jazyk**. Zadejte svůj klíč předplatného a potom vyberte **odeslat**.

  ![Termín – přidání parametry dotazu konzoly termín](images/try-terms-list-create-3.png)
 
10. Chcete-li ověřit, že termín byl přidán do seznamu, v nabídce vlevo vyberte **termín**a potom vyberte **získat všechny podmínky**. 

  **Termín - získat všechny podmínky** otevře se konzola rozhraní API.

11. V **listId** pole, zadejte ID seznamu a potom zadejte svůj klíč předplatného. Vyberte **Poslat**.

12. V **obsah odpovědi** ověřte podmínky, které jste zadali.

  ![Termín - Get všechny podmínky konzole odpovědi pole obsahu seznamy podmínky, které jste zadali](images/try-terms-list-create-4.png)
 
13. Přidáte pár další podmínky. Teď, když jste vytvořili vlastní seznam termínů, zkuste [kontrolu nějaký text](try-text-api.md) pomocí seznamu vlastní termín. 

## <a name="delete-terms-and-lists"></a>Odstranit podmínky a seznamy

Odstraněním termín nebo seznamu je jednoduchá. Prostřednictvím rozhraní API můžete provádět následující úlohy:

- Odstraňte termín. (**Termín – odstranit**)
- Odstraňte všechny podmínky v seznamu bez odstranění seznamu. (**Termín – odstranit všechny podmínky**)
- Odstraňte seznam a veškerý jeho obsah. (**Termín seznamy - odstranění**)

Tento příklad odstraní jeden termín.

1.  V [referenční dokumentace rozhraní API správy seznamu termín](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), v nabídce vlevo vyberte **termín**a potom vyberte **odstranit**. 

  **Termín – odstranit** otevře.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín - výběr oblast stránky odstranění](images/test-drive-region.png)

  **Termín – odstranit** otevře se konzola rozhraní API.
  
3.  V **listId** zadejte ID, které chcete odstranit termín ze seznamu. Toto ID je číslo (v našem příkladu **122**), je vrácený v **obsahuje termín - získat podrobnosti** konzoly pro MyList. Zadejte termín a vyberte jazyk.
 
  ![Termín - parametry dotazu odstranit konzoly](images/try-terms-list-delete-1.png)

4.  Zadejte svůj klíč předplatného a potom vyberte **odeslat**.

5.  Chcete-li ověřit, že byl odstraněn termín, použijte **termín uvádí - získat všechny** konzoly.

  ![Termín seznamy – všechny konzoly odpovědi obsahu získávat obsah, pole zobrazí, že se odstraní termín](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Informace o změně seznamu

Můžete upravit název a popis seznamu a přidejte položky metadat.

1.  V [referenční dokumentace rozhraní API správy seznamu termín](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), v nabídce vlevo vyberte **uvádí termín**a potom vyberte **podrobné informace o aktualizaci**. 

  **Termín obsahuje seznam - podrobnosti aktualizace** otevře se stránka.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Termín seznamy – vybrané oblasti podrobné informace o aktualizaci stránky](images/test-drive-region.png)

  **Termín obsahuje seznam - podrobnosti aktualizace** otevře se konzola rozhraní API.

3.  V **listId** pole, zadejte ID seznamu a potom zadejte svůj klíč předplatného.

4.  V **text žádosti** , provádět změny a pak vyberte **odeslat**.

  ![Termín seznamy – podrobnosti o aktualizacích konzole úpravy textu požadavku](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další postup

Použít rozhraní API REST v kódu ani začínat [termín uvádí rychlý start .NET](term-lists-quickstart-dotnet.md) integrovat s vaší aplikací.
