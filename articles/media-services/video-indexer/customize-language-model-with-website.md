---
title: Přizpůsobení jazykového modelu - Azure pomocí webových stránek Video Indexer
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak upravit jazykový model s webem Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: be0b930c99bb14c34e395efce494e8d7372719b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998136"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Upravit jazykový model s webem Video Indexer

Video Indexer umožňuje vytvářet vlastní jazykové modely přizpůsobit tak, že nahrajete přizpůsobení textu, a to textu z domény, jejichž slovní byste chtěli modul umožní reagovat na rozpoznávání řeči. Jakmile tréninku modelu, bude rozpoznán nová slova povolí, přizpůsobení textu. 

Podrobný přehled a osvědčené postupy pro vlastní jazykové modely, naleznete v tématu [upravit jazykový model s Video Indexer](customize-language-model-overview.md).

Můžete vytvářet a upravovat vlastní jazykové modely ve vašem účtu, můžete použít web Video Indexer, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v [přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvořit jazykový model

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Přizpůsobení modelu ve vašem účtu, klikněte na **obsahu přizpůsobení modelu** tlačítko v pravém horním rohu stránky.

   ![Přizpůsobení modelu obsahu](./media/content-model-customization/content-model-customization.png)

3. Vyberte **jazyk** kartu.

    Zobrazí seznam podporovaných jazyků. 

    ![Přizpůsobení jazykového modelu](./media/customize-language-model/customize-language-model.png)

4. Jazyk, který chcete, klikněte na tlačítko **přidat model**.
5. Zadejte název pro jazykový model a stiskněte enter.

    Tím se vytvoří model a nabízí možnost nahrát textové soubory do modelu.
6. Chcete-li přidat textový soubor, klikněte na tlačítko **přidat soubor**. Otevře Průzkumníka souborů.

7. Přejděte do a vyberte textový soubor. Můžete přidat více textových souborů do jazykového modelu.

    Můžete také přidat do textového souboru po kliknutí na **...**  tlačítko na pravé straně jazykový model a vyberete **přidat soubor**.
8. Po dokončení nahrávání textových souborů, klikněte na zelené **trénování** možnost.

    ![Trénování jazykový model](./media/customize-language-model/train-model.png)

Školení proces může trvat několik minut. Po školení se zobrazí **Trained** vedle modelu. Ve verzi preview, stáhněte si a odstranit soubor z modelu.

![Trénované jazykový model](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Pomocí jazykového modelu na nové video

Použití jazykového modelu na nové video, proveďte jednu z následujících akcí:

* Klikněte na **nahrát** tlačítko nahoře na stránce 

    ![Odeslat](./media/customize-language-model/upload.png)
* Vyřaďte zvukový nebo video soubor v kruhu nebo procházení k nahrání souboru

    ![Odeslat](./media/customize-language-model/upload2.png)

Tím získáte možnost vybrat si **Zdrojový jazyk videa**. Klikněte na rozevírací seznam a vyberte jazykový model, který jste vytvořili ze seznamu. Ji by měl Řekněme, že jazyk jazykového modelu a názvem, který jste zadali v závorkách.

Klikněte na tlačítko **nahrát** možnost v dolní části stránky a nové video bude indexovat pomocí jazykového modelu.

### <a name="using-a-language-model-to-reindex"></a>Pomocí jazykového modelu přeindexování

Použijte jazykový model přeindexování videa v kolekci, přejděte na vaše **účtu videa** na [Video Indexer](https://www.videoindexer.ai/) domovskou stránku a podržte ukazatel myši nad název video, které chcete přeindexování.

Zobrazí možnosti, jak upravovat vaše video, odstraňte vaši videa a přeindexování vaše video. Klikněte na možnost přeindexování vaše video.

![Reindex](./media/customize-language-model/reindex1.png)

Získáte možnost vybrat si **Zdrojový jazyk videa** přeindexování video. Klikněte na rozevírací seznam a vyberte jazykový model, který jste vytvořili ze seznamu. Ji by měl Řekněme, že jazyk jazykového modelu a názvem, který jste zadali v závorkách.

![Reindex](./media/customize-language-model/reindex.png)

Klikněte na tlačítko **znovu Indexujte** tlačítko a vaše video se bude přeindexovány pomocí jazykového modelu.

## <a name="edit-a-language-model"></a>Upravte jazykový model

Jazykový model můžete upravit tak, že změny jeho názvu, přidávání souborů do ní a odstraňování souborů z něj.

Je-li přidat nebo odstranit soubory z jazykový model, máte pro trénování modelu znovu kliknutím zelenou **trénování** možnost.

### <a name="rename-the-language-model"></a>Přejmenovat jazykový model

Kliknutím můžete změnit název jazykový model **...**  na pravé straně jazyka modelu a vyberete **přejmenovat**. 

Zadejte typ v nové jméno a přístupů.

### <a name="add-files"></a>Přidání souborů

Chcete-li přidat textový soubor, klikněte na tlačítko **přidat soubor**. Otevře Průzkumníka souborů.

Přejděte do a vyberte textový soubor. Můžete přidat více textových souborů do jazykového modelu.

Můžete také přidat do textového souboru po kliknutí na **...**  tlačítko na pravé straně jazykový model a vyberete **přidat soubor**.

### <a name="delete-files"></a>Odstranění souborů

Pokud chcete odstranit soubor z jazykový model, klikněte na tlačítko **...**  tlačítko na pravé straně textového souboru a vyberte **odstranit**. Otevře se nové okno oznamující, že odstranění nevratná. Klikněte na tlačítko **odstranit** možnost v novém okně.

Tato akce odebere soubor zcela z jazykový model.

## <a name="delete-a-language-model"></a>Odstranit jazykový model

Pokud chcete odstranit jazykový model z vašeho účtu, klikněte na tlačítko **...**  tlačítko na pravé straně jazykový model a vyberte **odstranit**.

Otevře se nové okno oznamující, že odstranění nevratná. Klikněte na tlačítko **odstranit** možnost v novém okně.

Tato akce odebere jazykového modelu zcela z vašeho účtu. Jakéhokoli videa, která se používala odstraněné jazykový model budete mít stejný index, dokud znovu Indexujte videa. Pokud znovu Indexujte videa, můžete přiřadit nového jazykového modelu na video. V opačném případě Video Indexer pomocí jeho výchozí model znovu Indexujte videa. 

## <a name="next-steps"></a>Další postup

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)
