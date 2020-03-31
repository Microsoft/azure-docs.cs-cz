---
title: Zaregistrujte se do video indexeru a nahrajte své první video - Azure
titleSuffix: Azure Media Services
description: Zjistěte, jak se zaregistrovat a nahrát na server svoje první video pomocí portálu Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: 22b06ce99333750d48d1cb65d9f60779572723b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499627"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Úvodní příručka: Jak se zaregistrovat a nahrát první video

Tento úvodní začátek začínáme ukazuje, jak se přihlásit na web Video Indexer a jak nahrát první video.

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou možností si vytvoříte účet Video Indexer, který je [připojený k vašemu předplatnému Azure a účtu Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Registrace k Video Indexeru

Pokud chcete začít s vývojem pomocí Video Indexeru, přejděte na web [Video Indexer](https://www.videoindexer.com) a zaregistrujte se.

> [!NOTE]
> Jakmile začnete používat Video Indexer, všechna uložená data a nahraný obsah jsou šifrovány v klidovém stavu pomocí spravovaného klíče společnosti Microsoft.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Nahrání videa na server pomocí webu Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Podporované formáty souborů pro video indexer

Seznam formátů souborů, které lze použít s indexerem videa, naleznete v článku [o formátech vstupních kontejnerů nebo souborů.](../latest/media-encoder-standard-formats.md#input-containerfile-formats)

### <a name="upload-a-video"></a>Nahrání videa

1. Přihlaste se k webu [Video Indexer](https://www.videoindexer.ai/).
2. Pokud chcete nahrát video na server, stiskněte tlačítko nebo odkaz **Upload** (Nahrát).

    > [!NOTE]
    > Název videa nesmí být větší než 80 znaků.

    ![Odeslat](./media/video-indexer-get-started/video-indexer-upload.png)

    Když se vaše video nahraje na server, začne ho Video Indexer indexovat a analyzovat.

    ![Nahráno](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Když Video Indexer dokončí analýzu, zobrazí se oznámení s odkazem na vaše video a krátkým popisem, co se ve videu našlo. Například: lidé, témata, OCR.

## <a name="see-also"></a>Viz také

Další [podrobnosti najdete v tématu Nahrávání a indexování videí.](upload-index-videos.md)

Po nahrání a indexování videa můžete začít používat web [Video Indexer](video-indexer-view-edit.md) nebo [Portál pro vývojáře videoindexeru,](video-indexer-use-apis.md) abyste viděli přehledy videa. 

[Začít používat api](video-indexer-use-apis.md)

## <a name="next-steps"></a>Další kroky

Pro podrobný úvod navštivte prosím naši [úvodní laboratoř](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Na konci workshopu budete mít dobré znalosti o druhu informací, které lze extrahovat z video a audio obsahu, budete více připraveni identifikovat příležitosti související s inteligencí obsahu, pitch video AI v Azure a demo několik scénáře na Video Indexer.

