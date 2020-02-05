---
title: Zaregistrujte se do Video Indexer a nahrajte své první video – Azure
titleSuffix: Azure Media Services
description: Zjistěte, jak se zaregistrovat a nahrát na server svoje první video pomocí portálu Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1f3d442ea7cf6d95427aaa9c072410119e2735af
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989803"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Rychlý Start: jak se zaregistrovat a nahrát své první video

Tento úvodní kurz ukazuje, jak se přihlásit k webu Video Indexer a jak nahrát na server svoje první video.

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. Pomocí placené možnosti vytvoříte účet Video Indexer, který je [připojený k vašemu předplatnému Azure a účet Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Registrace k Video Indexeru

Pokud chcete začít s vývojem pomocí Video Indexeru, přejděte na web [Video Indexer](https://www.videoindexer.com) a zaregistrujte se.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Nahrání videa na server pomocí webu Video Indexer

> [!NOTE]
> Název videa nesmí být delší než 80 znaků.

### <a name="supported-file-formats-for-video-indexer"></a>Podporované formáty souborů pro Video Indexer

Seznam formátů souborů, které můžete použít s Video Indexer, najdete v článku o [formátech vstupního kontejneru/souboru](../latest/media-encoder-standard-formats.md#input-containerfile-formats) .

### <a name="upload-a-video"></a>Nahrání videa

1. Přihlaste se k webu [Video Indexer](https://www.videoindexer.ai/).
2. Pokud chcete nahrát video na server, stiskněte tlačítko nebo odkaz **Upload** (Nahrát).

    ![Nahrávání](./media/video-indexer-get-started/video-indexer-upload.png)

    Když se vaše video nahraje na server, začne ho Video Indexer indexovat a analyzovat.

    ![Nahráno](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Když Video Indexer dokončí analýzu, zobrazí se oznámení s odkazem na vaše video a krátkým popisem, co se ve videu našlo. Například: lidé, témata, OCR.

## <a name="see-also"></a>Další informace najdete v tématech

Další podrobnosti najdete v tématu [videa pro nahrávání a indexování](upload-index-videos.md) .

Po nahrání a indexování videa můžete začít používat web [video indexer](video-indexer-view-edit.md) nebo [portál pro vývojáře video indexer](video-indexer-use-apis.md) , abyste viděli přehledy videa. 

[Začínáme používat rozhraní API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Další kroky

Podrobný Úvod najdete v našem [testovacím prostředí](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Na konci dílny budete mít dobrý přehled o druhu informací, které je možné extrahovat z videa a zvukového obsahu, budete připraveni k identifikaci příležitostí souvisejících s funkcí Content Intelligence, stoupání videa AI v Azure a Ukázka několika scénáře na Video Indexer.

