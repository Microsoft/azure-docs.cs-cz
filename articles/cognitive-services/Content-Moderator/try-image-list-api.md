---
title: Střední obrázků s použitím vlastních seznamů, které v obsahu moderátora Azure | Microsoft Docs
description: Test-Drive seznamy vlastní image v konzole obsahu moderátora rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2d714f017be16d978ffbb877a2b7e78e1caf9169
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342512"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Střední pomocí seznamů vlastní image v konzole rozhraní API

Můžete použít [rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) v obsahu moderátora Azure k vytvoření vlastních seznamů obrázků. Pomocí vlastních seznamů obrázků s rozhraním API přerušování bitové kopie. Operace přerušování bitové kopie vyhodnotí bitové kopie. Pokud vytvoříte vlastní seznamy, operace také porovná ho obrázků v vlastních seznamů. Vlastních seznamů, které můžete použít k blokování nebo povolení bitovou kopii.

> [!NOTE]
> Maximální limit je **5 obrázku seznamy** s každou seznamu **není delší než 10 000 image**.
>

Pomocí rozhraní API pro správu seznamu provést následující úlohy:

- Vytvoří seznam.
- Přidání bitové kopie do seznamu.
- Obrazovky bitové kopie s obrázky v seznamu.
- Bitové kopie odstraňte ze seznamu.
- Odstranění seznamu.
- Upravte informace o seznamu.
- Aktualizujte index, aby se změny provedené v seznamu jsou zahrnuté v nové prohledání.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Než můžete test-drive rozhraní API v konzole online, musíte svůj klíč předplatného. To se nachází na **nastavení** ve **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizovat index vyhledávání

Když provedete změny seznamu obrázků, je nutné aktualizovat její index pro změny, které mají být zahrnuty v budoucích kontrolách. Tento krok je podobný jak vyhledávacího webu na ploše (Pokud je povoleno) nebo vyhledávací web průběžně aktualizuje její index zahrnout nových souborů nebo stránky.

1.  V [referenční dokumentace rozhraní API správy seznamu Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), v nabídce vlevo vyberte **seznamy obrázků**a potom vyberte **aktualizovat Index vyhledávání**.

  **Seznamy obrázků - aktualizovat Index vyhledávání** otevře se stránka.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 
 
    ![Seznamy obrázků - výběr oblast stránky indexu vyhledávání aktualizace](images/test-drive-region.png)

    **Seznamy obrázků - aktualizovat Index vyhledávání** otevře se konzola rozhraní API.

3.  V **listId** zadejte ID seznamu. Zadejte svůj klíč předplatného a potom vyberte **odeslat**.

  ![Seznamy obrázků – Index vyhledávání aktualizace konzoly pole obsahu odpovědi](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Vytvoření seznamu obrázků

1.  Přejděte na [referenční dokumentace rozhraní API správy seznamu Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  **Seznamy obrázků - vytvořit** otevře se stránka. 

3.  Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.

    ![Obrázek seznamy – vytvořit výběr oblast stránky](images/test-drive-region.png)

    **Seznamy obrázků - vytvořit** otevře se konzola rozhraní API.
 
4.  V **Ocp-Apim-Subscription-Key** zadejte svůj klíč předplatného.

5.  V **text žádosti** zadejte hodnoty pro **název** (například MyList) a **popis**.

  ![Obrázek seznamy – vytvoření konzoly požadavek textu název a popis](images/try-terms-list-create-1.png)

6.  Použijte zástupné symboly dvojice klíč hodnota přiřadit více popisná metadata do seznamu.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Přidejte seznam metadat jako páry klíč hodnota a ne skutečné bitové kopie.
 
7.  Vyberte **Poslat**. Vytvoří se váš seznam. Poznámka: **ID** hodnotu, která souvisí s nového seznamu. Musíte toto ID pro další funkce správy seznamu bitové kopie.

  ![Obrázek seznamy – vytvoření konzoly odpovědi obsahu pole se zobrazí seznam ID](images/try-terms-list-create-2.png)
 
8.  Potom do MyList přidejte bitové kopie. V nabídce vlevo vyberte **Image**a potom vyberte **přidat bitovou**.

  **Obrázku – Přidání bitové kopie** otevře se stránka. 

9. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Obrázek – přidat výběr oblasti obrázku stránky](images/test-drive-region.png)

  **Obrázku – Přidání bitové kopie** otevře se konzola rozhraní API.
 
10. V **listId** pole, zadejte ID seznamu, který jste vygenerovali a potom zadejte adresu URL obrázku, který chcete přidat. Zadejte svůj klíč předplatného a potom vyberte **odeslat**.

11. Chcete-li ověřit, že obrázek byl přidán do seznamu, v nabídce vlevo vyberte **Image**a potom vyberte **získat všechna ID bitové kopie**.

  **Image - získat všechna ID bitové kopie** otevře se konzola rozhraní API.
  
12. V **listId** pole, zadejte ID seznamu a potom zadejte svůj klíč předplatného. Vyberte **Poslat**.

  ![Obrázek – všechna ID bitové kopie Get konzole odpovědi pole obsahu uvádí bitové kopie, které jste zadali](images/try-image-list-create-11.png)
 
10. Přidejte další pár Image. Teď, když jste vytvořili vlastní seznam bitové kopie, zkuste [vyhodnocení bitové kopie](try-image-api.md) pomocí seznamu vlastní image. 

## <a name="delete-images-and-lists"></a>Odstranit Image a seznamy

Odstranění obrázku nebo seznamu je jednoduchá. Rozhraní API můžete provést následující úkoly:

- Odstranění image. (**Obrázek – odstranit**)
- Odstraňte všechny Image v seznamu bez odstranění seznamu. (**Obrázek – odstraňte všechny image**)
- Odstraňte seznam a veškerý jeho obsah. (**Seznamů obrázků - odstranění**)

Tento příklad odstraní jeden obrázek:

1. V [referenční dokumentace rozhraní API správy seznamu Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), v nabídce vlevo vyberte **Image**a potom vyberte **odstranit**. 

  **Obrázek – odstranit** otevře se stránka.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Obrázek – odstranit vybrané oblasti stránky](images/test-drive-region.png)
 
  **Obrázek – odstranit** otevře se konzola rozhraní API.
 
3.  V **listId** zadejte ID seznamu odstranit bitovou kopii.  Toto je počet vrátí **Image - získat všechna ID bitové kopie** konzoly pro MyList. Potom zadejte **ID obrázku** bitové kopie odstranit. 

V našem příkladu je ID seznamu **58953**, hodnota **ContentSource**. Bitovou kopii je ID **59021**, hodnota **ContentIds**.

4.  Zadejte svůj klíč předplatného a potom vyberte **odeslat**.

5.  Chcete-li ověřit, že byla odstraněna bitovou kopii, použijte **Image - získat všechna ID bitové kopie** konzoly.
 
## <a name="change-list-information"></a>Informace o změně seznamu

Můžete upravit název a popis seznamu a přidejte položky metadat.

1.  V [referenční dokumentace rozhraní API správy seznamu Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), v nabídce vlevo vyberte **seznamy obrázků**a potom vyberte **podrobné informace o aktualizaci**. 

  **Seznamy obrázků – podrobnosti o aktualizacích** otevře se stránka.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.  

    ![Seznamy obrázků - výběr oblast stránky podrobné informace o aktualizaci](images/test-drive-region.png)

    **Seznamy obrázků – podrobnosti o aktualizacích** otevře se konzola rozhraní API.
 
3.  V **listId** pole, zadejte ID seznamu a potom zadejte svůj klíč předplatného.

4.  V **text žádosti** , provádět změny a pak vyberte **odeslat** tlačítko na stránce.

  ![Seznamy obrázků - podrobnosti aktualizace konzoly úpravy textu požadavku](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další postup

Použít rozhraní API REST v kódu ani začínat [seznamy obrázků rychlý start .NET](image-lists-quickstart-dotnet.md) integrovat s vaší aplikací.
