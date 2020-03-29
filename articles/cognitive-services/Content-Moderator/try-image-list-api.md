---
title: Moderování obrázků s vlastními seznamy a konzolou rozhraní API – Moderátor obsahu
titleSuffix: Azure Content Moderator
description: Rozhraní API pro správu seznamů v Moderátoru obsahu Azure se používá k vytvoření vlastních seznamů ibi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757197"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderovat s vlastními seznamy obrázků v konzole rozhraní API

Rozhraní [API pro správu seznamů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) v Moderátoru obsahu Azure se používá k vytvoření vlastních seznamů ibi. Pomocí vlastních seznamů obrázků s rozhraním API pro moderování obrázků. Operace moderování obrazu vyhodnotí váš obraz. Pokud vytvoříte vlastní seznamy, operace jej také porovná s obrázky ve vlastních seznamech. K zablokování nebo povolení obrázku můžete použít vlastní seznamy.

> [!NOTE]
> Limit je maximálně **pět seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

Rozhraní API pro správu seznamů se používá k následujícím úkolům:

- Vytvoření seznamu
- Přidejte obrázky do seznamu.
- Obraz obraz oslnění obrázky v seznamu.
- Odstranění obrázků ze seznamu.
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

## <a name="use-the-api-console"></a>Použití konzoly rozhraní API
Než budete moci testovací disk API v online konzole, budete potřebovat klíč předplatného. To se nachází na kartě **Nastavení,** v **poli Ocp-Apim-Subscription-Key.** Další informace najdete v tématu [Přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizovat index vyhledávání

Po provádění změn v seznamu obrázků je nutné aktualizovat jeho index, aby změny byly zahrnuty do budoucích prohledávek. Tento krok je podobný tomu, jak vyhledávač na ploše (pokud je povolen) nebo webový vyhledávač neustále aktualizuje svůj index, aby zahrnoval nové soubory nebo stránky.

1. V [odkazu rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)vyberte v levé nabídce **seznamy obrázků**a pak vyberte Aktualizovat index **hledání**.

   Otevře se stránka **Seznamy obrázků – Aktualizovat index vyhledávání.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 
 
    ![Seznamy obrázků – výběr oblasti aktualizovat index hledání](images/test-drive-region.png)

    Otevře se **konzola Image Lists - Refresh Search Index** API .

3. Do pole **listId** zadejte ID seznamu. Zadejte klíč předplatného a pak vyberte **Odeslat**.

   ![Seznamy obrázků – pole Aktualizovat obsah konzoly Search Index](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Vytvoření seznamu obrázků

1. Přejděte na [odkaz rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Otevře se **stránka Seznam obrázků – Vytvořit** stránku. 

3. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu.

   ![Seznamy obrázků – vytvoření výběru oblasti stránky](images/test-drive-region.png)

   Otevře se **seznamy obrázků – vytvoření** konzoly rozhraní API.
 
4. Do pole **Ocp-Apim-Subscription-Key** zadejte klíč předplatného.

5. Do pole **Text požadavku** zadejte hodnoty pro **Název** (například MyList) a **Description**.

   ![Seznamy obrázků – vytvoření názvu a popisu těla požadavku na konzolu](images/try-terms-list-create-1.png)

6. Pomocí zástupných symbolů pro párování klíč-hodnota přiřaďte do seznamu popisnější metadata.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Přidejte metadata seznamu jako dvojice klíč-hodnota a ne skutečné obrázky.
 
7. Vyberte **Poslat**. Váš seznam je vytvořen. Poznamenejte si hodnotu **ID,** která je přidružena k novému seznamu. Toto ID potřebujete pro další funkce správy seznamu obrázků.

   ![Seznamy obrázků – pole Vytvořit obsah odpovědi konzoly zobrazuje ID seznamu.](images/try-terms-list-create-2.png)
 
8. Dále přidejte obrázky do seznamu MyList. V levé nabídce vyberte **Obraz**a pak **vyberte Přidat obraz**.

   Otevře se stránka **Obrázek - Přidat obrázek.** 

9. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu.

   ![Obrázek – výběr oblasti stránky Obrázek](images/test-drive-region.png)

   Otevře se **konzola Image - Add Image** API .
 
10. Do pole **listId** zadejte ID seznamu, který jste vygenerovali, a zadejte adresu URL obrázku, který chcete přidat. Zadejte klíč předplatného a pak vyberte **Odeslat**.

11. Chcete-li ověřit, zda byl obrázek přidán do seznamu, vyberte v levé nabídce **možnost Obraz**a pak vyberte možnost Získat **všechna ID obrázků**.

    Otevře se konzola Rozhraní API **Image - Get All Image Ids.**
  
12. Do pole **listId** zadejte ID seznamu a zadejte klíč předplatného. Vyberte **Poslat**.

    ![Obrázek - Získat všechny image Ids konzole Odpověď obsah box uvádí obrázky, které jste zadali](images/try-image-list-create-11.png)
 
10. Přidejte několik dalších obrázků. Nyní, když jste vytvořili vlastní seznam obrázků, zkuste [vyhodnotit obrázky](try-image-api.md) pomocí vlastního seznamu obrázků. 

## <a name="delete-images-and-lists"></a>Odstranění obrázků a seznamů

Odstranění obrázku nebo seznamu je jednoduché. Rozhraní API můžete použít k následujícím úkolům:

- Odstranění image. (**Obrázek - Odstranit**)
- Odstraňte všechny obrázky v seznamu bez odstranění seznamu. **(Obrázek - Odstranit všechny obrázky**)
- Odstranění seznamu a veškerého jeho obsahu. (**Seznamy obrázků - Odstranit**)

Tento příklad odstraní jeden obrázek:

1. V [odkazu rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)vyberte v levé nabídce **obrázek**a pak vyberte **Odstranit**. 

   Otevře se stránka **Obrázek - Odstranit.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Obrázek – odstranit výběr oblasti stránky](images/test-drive-region.png)
 
   Otevře se **konzola Image - Delete** API.
 
3. Do pole **listId** zadejte ID seznamu, ze které chcete obrázek odstranit.  Toto je číslo vrácené v **image - Získat všechna id obrázků** konzole pro MyList. Potom zadejte **ImageId** obrázku odstranit. 

V našem příkladu je ID seznamu **58953**, hodnota **pro ContentSource**. ID obrázku je **59021**, hodnota **ContentIds**.

1. Zadejte klíč předplatného a pak vyberte **Odeslat**.

1. Chcete-li ověřit, zda byla bitová kopie odstraněna, použijte konzolu **Image - Get All Image Ids.**
 
## <a name="change-list-information"></a>Změna informací o seznamu

Můžete upravit název a popis seznamu a přidat položky metadat.

1. V [odkazu rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)vyberte v levé nabídce **seznamy obrázků**a pak vyberte Aktualizovat **podrobnosti**. 

   Otevře se stránka **Seznamy obrázků – Aktualizovat podrobnosti.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu.  

    ![Seznamy obrázků – výběr oblasti stránky S podrobnostmi o aktualizaci](images/test-drive-region.png)

    Otevře se **konzola Image Lists - Update Details** API console .
 
3. Do pole **listId** zadejte ID seznamu a zadejte klíč předplatného.

4. V poli **Vyžádat tělo** proveďte úpravy a pak na stránce vyberte tlačítko **Odeslat.**

   ![Seznamy obrázků – aktualizace podrobností konzoly Požadavku na úpravy těla](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další kroky

Pomocí rozhraní REST API v kódu nebo začněte s [panelem rychlý start seznamů obrázků .NET](image-lists-quickstart-dotnet.md) a integrujte se s vaší aplikací.
