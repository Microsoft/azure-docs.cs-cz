---
title: Střední obrázky s vlastními seznamy a konzolou rozhraní API – Content Moderator
titleSuffix: Azure Content Moderator
description: Pomocí rozhraní API pro správu seznamu v Azure Content Moderator můžete vytvářet vlastní seznamy imagí.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85800144"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Střední s vlastními seznamy obrázků v konzole API

Pomocí [rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) v Azure Content moderator můžete vytvářet vlastní seznamy imagí. Použijte vlastní seznamy imagí s rozhraním API pro moderování imagí. Operace moderování obrázku vyhodnocuje váš obrázek. Pokud vytvoříte vlastní seznamy, tato operace také porovná obrázek ve vlastních seznamech. K blokování nebo povolení image můžete použít vlastní seznamy.

> [!NOTE]
> Limit je maximálně **pět seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

Pomocí rozhraní API pro správu seznamu můžete provádět následující úlohy:

- Vytvoření seznamu
- Přidejte obrázky do seznamu.
- Obrázky obrazovky proti obrázkům v seznamu.
- Odstraní obrázky ze seznamu.
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

## <a name="use-the-api-console"></a>Použití konzoly API
Než budete moct otestovat rozhraní API v online konzole, budete potřebovat svůj klíč předplatného. Najdete ho na kartě **Nastavení** v poli **OCP-APIM-Subscription-Key** . Další informace najdete v tématu [Přehled](overview.md).

## <a name="refresh-search-index"></a>Aktualizovat index vyhledávání

Až provedete změny v seznamu obrázků, je nutné aktualizovat index, aby byly změny zahrnuté do budoucích kontrol. Tento krok je podobný tomu, jak vyhledávací modul na ploše (Pokud je povolen) nebo webový vyhledávací modul průběžně aktualizuje svůj index, aby zahrnoval nové soubory nebo stránky.

1. V [referenčních informacích k rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)v nabídce vlevo vyberte **seznamy obrázků**a pak vyberte **Aktualizovat index vyhledávání**.

   Otevře se stránka **seznam obrázků – index hledání obnovení** .

2. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění. 
 
    ![Seznamy obrázků – aktualizovat výběr oblasti stránky indexu hledání](images/test-drive-region.png)

    Otevře se seznam imagí – spustí se konzola rozhraní API **pro obnovení indexu vyhledávání** .

3. Do pole **listId** zadejte ID seznamu. Zadejte svůj klíč předplatného a pak vyberte **Odeslat**.

   ![Seznamy obrázků – aktualizovat obsah odpovědi konzoly indexu hledání](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Vytvoření seznamu obrázků

1. Přejít na [Reference k rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Otevře se **seznam obrázků – vytvořit** stránku. 

3. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění.

   ![Seznamy obrázků – výběr oblasti vytvoření stránky](images/test-drive-region.png)

   Otevře se **seznam imagí – vytvoří** se konzola rozhraní API.
 
4. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

5. Do pole **Text žádosti** zadejte hodnoty pro **Name** (například myList) a **Description (popis**).

   ![Seznamy obrázků – název a popis žádosti o vytvoření konzoly](images/try-terms-list-create-1.png)

6. Použijte zástupné symboly dvojice klíč-hodnota k přiřazení dalších popisných metadat k seznamu.

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

   Přidejte metadata seznamu jako páry klíč-hodnota, nikoli skutečné obrázky.
 
7. Vyberte **Poslat**. Vytvoří se Váš seznam. Poznamenejte si hodnotu **ID** , která je přidružená k novému seznamu. Toto ID budete potřebovat pro další funkce správy seznamu obrázků.

   ![Seznamy obrázků – okno vytvořit obsah odpovědi konzoly zobrazuje ID seznamu](images/try-terms-list-create-2.png)
 
8. Dále přidejte obrázky do MyList. V nabídce vlevo vyberte **Obrázek**a pak vyberte **Přidat obrázek**.

   Otevře se stránka **obrázek – přidat obrázek** . 

9. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění.

   ![Obrázek – přidat výběr oblasti stránky obrázku](images/test-drive-region.png)

   Otevře se konzola rozhraní API **pro přidání** image.
 
10. Do pole **listId** zadejte ID seznamu, které jste vygenerovali, a pak zadejte adresu URL obrázku, který chcete přidat. Zadejte svůj klíč předplatného a pak vyberte **Odeslat**.

11. Chcete-li ověřit, zda byla obrázek přidána do seznamu, vyberte v nabídce vlevo položku **Obrázek**a potom vyberte možnost **získat všechna ID imagí**.

    Otevře se konzola rozhraní API **image – získat všechna ID imagí** .
  
12. Do pole **listId** zadejte ID seznamu a potom zadejte svůj klíč předplatného. Vyberte **Poslat**.

    ![Image – získat všechna ID imagí v konzole obsah odpovědi konzoly seznam imagí, které jste zadali](images/try-image-list-create-11.png)
 
10. Přidejte několik dalších imagí. Teď, když jste vytvořili vlastní seznam imagí, zkuste [vyhodnotit image](try-image-api.md) pomocí vlastního seznamu obrázků. 

## <a name="delete-images-and-lists"></a>Odstranění obrázků a seznamů

Odstranění obrázku nebo seznamu je jednoduché. Pomocí tohoto rozhraní API můžete provádět následující úlohy:

- Odstranění image. (**Bitová kopie – odstranění**)
- Odstraní všechny obrázky v seznamu bez odstranění seznamu. (**Obrázek – odstranění všech imagí**)
- Odstranění seznamu a veškerého jeho obsahu. (**Seznamy obrázků-odstranit**)

Tento příklad odstraní jeden obrázek:

1. V [referenčních informacích k rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)vyberte v nabídce vlevo možnost **Obrázek**a pak vyberte **Odstranit**. 

   Otevře se stránka **Obrázek – odstranit** .

2. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Obrázek – výběr oblasti stránky pro odstranění](images/test-drive-region.png)
 
   Otevře se konzola rozhraní API **pro odstranění imagí** .
 
3. V poli **listId** zadejte ID seznamu, ze kterého se má obrázek odstranit.  Toto je číslo vrácené v konzole **image – získat všechna ID imagí** pro myList. Pak zadejte **ImageId** obrázku, který chcete odstranit. 

V našem příkladu je ID seznamu **58953**, hodnota pro **ContentSource**. ID Image je **59021**, hodnota pro **ContentIds**.

1. Zadejte svůj klíč předplatného a pak vyberte **Odeslat**.

1. Pokud chcete ověřit, že se image odstranila, použijte konzolu **image – získat všechna ID imagí** .
 
## <a name="change-list-information"></a>Změnit informace o seznamu

Můžete upravit název a popis seznamu a přidat položky metadat.

1. V [referenčních informacích k rozhraní API pro správu seznamu obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)v nabídce vlevo vyberte **seznamy obrázků**a pak vyberte **aktualizovat podrobnosti**. 

   Otevře se stránka **seznamy obrázků – podrobnosti o aktualizaci** .

2. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění.  

    ![Seznamy obrázků – výběr oblasti stránky s podrobnostmi aktualizace](images/test-drive-region.png)

    Otevře se **seznam obrázků – konzola rozhraní API s podrobnostmi o aktualizaci** .
 
3. Do pole **listId** zadejte ID seznamu a potom zadejte svůj klíč předplatného.

4. V poli **Text žádosti** proveďte úpravy a pak na stránce vyberte tlačítko **Odeslat** .

   ![Seznamy obrázků – aktualizace těla žádosti konzoly úpravy textu](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Další kroky

Pomocí REST API v kódu nebo začněte s [imagí Seznamte se s rychlým](image-lists-quickstart-dotnet.md) startem .NET pro integraci s vaší aplikací.
