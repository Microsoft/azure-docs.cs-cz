---
title: Přizpůsobení modelu osobě – Azure pomocí webových stránek Video Indexer
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak upravit model osoba s webem Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997052"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Přizpůsobení modelu osoba s webem Video Indexer

Video Indexer podporuje rozpoznávání tváří a rozpoznávání celebrit v případě video obsahu. Funkce rozpoznávání celebrit pokrývá přibližně jednoho milionu tváří, které jsou založené na zdroji dat běžně požadovaných například IMDB Wikipedia a horní vlivné osoby LinkedIn. Zjištění tváří, které nejsou rozpoznány aplikací funkce rozpoznávání celebrit; Nicméně jsou ponechána bez názvu. Po nahrání videa do modulu Video Indexer a získat výsledky zpět, můžete přejít zpět a pojmenujte tváří, které nebyly rozpoznány. Jakmile je označování plochy s názvem pro rozpoznávání tváře a název nechejte se přidat do vašeho účtu osoba modelu. Video Indexer pak zjistit tento tvář budoucí videa a posledních videa.

Video Indexer webu můžete použít k úpravě tváří, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v [vlastní osoba modelu s použitím rozhraní API](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>Upravit rozpoznávání tváře

> [!NOTE]
> Názvy jsou jedinečné pro v modelech osoby, takže pokud pojmenujete dva různé čelí se stejným názvem, Video Indexer zobrazení plochy jako stejné osobě a sladila je po přeindexování vaše video. Pokud zjistíte, že Video Indexer byla nalezena více různých výskyty stejného rozpoznávání tváře, uživatelům umožnit se stejným názvem a přeindexování vaše video.
> Můžete aktualizovat řez, který Video Indexer rozpoznán jako celebrit s novým názvem. Název, kterým dáváte přednost rozpoznávání celebrit integrované.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Vyhledejte video, které chcete zobrazit a upravit ve vašem účtu.
3. Rozpoznávání tváře ve videu upravit, přejděte **Insights** kartě a klikněte na ikonu tužky v pravém horním rohu okna.

    ![Přizpůsobení modelu osoby](./media/customize-face-model/customize-face-model.png)

4. Klepněte na žádný zjištěný tváří a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazen typ písma).
5. Po zadání nový název, klikněte na ikonu zaškrtnutí vedle názvu nové. To bude uložte nový název a rozpoznat a pojmenujte všechny výskyty tohoto tvář vaše další aktuální videa a v budoucnu videa, které nahrajete. Rozpoznávání tváře ve vaší aktuální videa může trvat nějakou dobu projevily, je to dávkového procesu. 

    ![Uložit aktualizace](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>Odstranit plochy

Odstranit zjištěný rozpoznávání tváře ve videu, přejděte na **Insights** podokně a kliknutím na ikonu tužky v pravém horním rohu podokna. Klikněte na tlačítko **odstranit** možnost pod názvem typ písma. Tato akce odebere zjištěným tváří z videa. Typ písma bude stále rozpoznán v jiných videí, ve které jsou zobrazeny, ale obličej můžete odstranit z těchto videí a po byla indexovat. Typ písma také budou existovat v modelu osoba váš účet, pokud ji pojmenujete před jejím odstraněním.

## <a name="next-steps"></a>Další postup

[Přizpůsobení modelu osoba s použitím rozhraní API](customize-person-model-with-api.md)
