---
title: Moderování obrázků pomocí vlastních seznamů a rozhraní API konzoly – Content Moderator
titlesuffix: Azure Content Moderator
description: Rozhraní API pro správu seznamu v Azure Content Moderator použijete k vytvoření vlastních seznamů obrázků.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e28342b2f2a4846f80940e701dfb638e8f860e5c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864345"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Střední s vlastním seznamem obrázků v konzole pro rozhraní API

Můžete použít [rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) v Azure Content Moderator k vytvoření vlastních seznamů obrázků. Vlastní seznam imagí pomocí rozhraní API pro moderování obrázků. Operace pro moderování obrázků vyhodnotí bitové kopie. Pokud vytvoříte vlastní seznamy, operace se taky porovnává do bitové kopie ve vlastních seznamů. Vlastní seznamy můžete použít k blokování nebo povolení bitovou kopii.

> [!NOTE]
> Limit je maximálně **5 seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

Rozhraní API pro správu seznamu můžete provádět následující úlohy:

- Vytvoření seznamu
- Přidání obrázků do seznamu.
- Obrázky obrazovky s obrázky v seznamu.
- Odstraňte Image ze seznamu.
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Předtím, než můžete vyzkoušet rozhraní API v konzole online, budete potřebovat klíč předplatného. To je umístěn na **nastavení** kartě **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [Přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizujte index vyhledávání

Když provedete změny seznamu obrázků, je nutné aktualizovat jeho index pro změny, které mají být zahrnuty v budoucích kontrolách. Tento krok je podobný jak vyhledávacího webu na ploše (je-li povoleno) nebo webového vyhledávacího webu průběžně aktualizuje jeho index nových souborů nebo stránky.

1.  V [referenční dokumentace rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), v nabídce vlevo vyberte **seznamy obrázků**a pak vyberte **aktualizovat Index vyhledávání**.

  **Seznamy obrázků – aktualizovat Index vyhledávání** otevře se stránka.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 
 
    ![Seznamy obrázků – výběr oblasti stránek indexu vyhledávání aktualizace](images/test-drive-region.png)

    **Seznamy obrázků – aktualizovat Index vyhledávání** otevře se konzola rozhraní API.

3.  V **listId** zadejte ID seznamu. Zadejte klíč předplatného a pak vyberte **odeslat**.

  ![Seznamy obrázků - Index vyhledávání aktualizace konzoly pole obsahu odpovědi](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Vytvoření seznamu obrázků

1.  Přejděte [referenční dokumentace rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  **Seznamy obrázků – vytvořit** otevře se stránka. 

3.  Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.

    ![Seznamy obrázků: vytvoření stránky oblast výběru](images/test-drive-region.png)

    **Seznamy obrázků – vytvořit** otevře se konzola rozhraní API.
 
4.  V **Ocp-Apim-Subscription-Key** zadejte váš klíč předplatného.

5.  V **text žádosti** , zadejte hodnoty pro pole **název** (například MyList) a **popis**.

  ![Seznamy obrázků – vytvoření konzoly žádost subjektu název a popis](images/try-terms-list-create-1.png)

6.  Pomocí páru klíč hodnota zástupné symboly můžete přiřadit více popisná metadata do seznamu.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Přidáte seznam metadat jako páry klíč hodnota a ne skutečné bitové kopie.
 
7.  Vyberte **Poslat**. Vytvoření seznamu. Poznámka: **ID** hodnotu, která je přidružena k nového seznamu. Toto ID budete potřebovat další funkce správy seznamu obrázků.

  ![Seznamy obrázků – vytvoření konzoly odpovědi obsahu pole se zobrazí seznam ID](images/try-terms-list-create-2.png)
 
8.  V dalším kroku přidáte Image do MyList. V nabídce vlevo vyberte **Image**a pak vyberte **přidat obrázek**.

  **Image - přidat bitovou kopii** otevře se stránka. 

9. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Obrázek – přidat výběr oblasti obrázku stránky](images/test-drive-region.png)

  **Image - přidat bitovou kopii** otevře se konzola rozhraní API.
 
10. V **listId** zadejte ID seznamu, který jste vygenerovali a potom zadejte adresu URL obrázku, který chcete přidat. Zadejte klíč předplatného a pak vyberte **odeslat**.

11. Chcete-li ověřit, že image byla přidána do seznamu, v nabídce vlevo vyberte **Image**a pak vyberte **získáte všechna ID Image**.

  **Image - získáte všechna ID Image** otevře se konzola rozhraní API.
  
12. V **listId** zadejte ID seznamu a pak zadejte klíč předplatného. Vyberte **Poslat**.

  ![Obrázek – Get všechny Image ID konzole odpovědi obsahu pole obsahuje seznam imagí, které jste zadali](images/try-image-list-create-11.png)
 
10. Přidáte pár další Image. Teď, když vytvoříte vlastní seznam imagí, zkuste [vyhodnocování image](try-image-api.md) pomocí seznamu vlastní image. 

## <a name="delete-images-and-lists"></a>Odstranit Image a seznamy

Odstraňuje se obrázek nebo seznamu je jednoduché. Rozhraní API můžete provádět následující úlohy:

- Odstranění image. (**Obrázek – odstranit**)
- Odstraňte všechny Image v seznamu bez odstranění seznamu. (**Obrázek – odstranit všechny image**)
- Odstranění seznamu a veškerého jeho obsahu. (**Seznamy obrázků - Delete**)

Tento příklad odstraní jedné image:

1. V [referenční dokumentace rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), v nabídce vlevo vyberte **Image**a pak vyberte **odstranit**. 

  **Obrázek – odstranit** otevře se stránka.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Obrázek – výběr oblasti stránek Delete](images/test-drive-region.png)
 
  **Obrázek – odstranit** otevře se konzola rozhraní API.
 
3.  V **listId** , zadejte ID seznamu, který se má odstranit bitové kopie z pole.  Toto je číslo vrácen v **Image - získáte všechna ID Image** konzoly, kde MyList. Potom zadejte **ID obrázku** bitové kopie odstranit. 

V našem příkladu je ID seznamu **58953**, hodnota **ContentSource**. Na obrázku je ID **59021**, hodnota **ContentIds**.

4.  Zadejte klíč předplatného a pak vyberte **odeslat**.

5.  Chcete-li ověřit, že image byla odstraněna, použijte **Image - získáte všechna ID Image** konzoly.
 
## <a name="change-list-information"></a>Informace o změně seznamu

Můžete upravit název a popis seznamu a přidat položky metadat.

1.  V [referenční dokumentace rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), v nabídce vlevo vyberte **seznamy obrázků**a pak vyberte **podrobnosti o aktualizacích**. 

  **Image obsahuje seznam - podrobnosti o aktualizacích** otevře se stránka.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.  

    ![Seznamy obrázků – podrobné informace o aktualizaci stránky oblast výběru](images/test-drive-region.png)

    **Image obsahuje seznam - podrobnosti o aktualizacích** otevře se konzola rozhraní API.
 
3.  V **listId** zadejte ID seznamu a pak zadejte klíč předplatného.

4.  V **text žádosti** udělejte úpravy a potom vyberte **odeslat** tlačítko na stránce.

  ![Seznamy obrázků – podrobnosti o aktualizacích konzole úpravy textu požadavku](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [Image obsahuje rychlý úvod k .NET](image-lists-quickstart-dotnet.md) integrovat s vaší aplikací.
