---
title: Zaregistrujte se do Video Indexer a nahrajte své první video – Azure
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
ms.openlocfilehash: f6c3953947e6f7e84f4cf9b565d2f66648b177f7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85130751"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Rychlý Start: jak se zaregistrovat a nahrát své první video

V tomto rychlém startu se dozvíte, jak se přihlásit k webu Video Indexer a jak nahrát první video.

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou variantou vytvoříte účet Video Indexeru, který je [spojený s vaším předplatným Azure a účtem služby Azure Media Services](connect-to-azure.md). Platíte za indexované minuty a také poplatky související s účtem Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Registrace k Video Indexeru

Pokud chcete začít s vývojem pomocí Video Indexeru, přejděte na web [Video Indexer](https://www.videoindexer.ai/) a zaregistrujte se.

> [!NOTE]
> Po zahájení používání Video Indexer se všechna uložená data a nahraná data zašifrují v klidovém formátu pomocí spravovaného klíče Microsoftu.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Nahrání videa na server pomocí webu Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Podporované formáty souborů pro Video Indexer

Seznam formátů souborů, které můžete použít s Video Indexer, najdete v článku o [formátech vstupního kontejneru/souboru](../latest/media-encoder-standard-formats.md#input-containerfile-formats) .

### <a name="upload-a-video"></a>Nahrání videa

1. Přihlaste se k webu [Video Indexer](https://www.videoindexer.ai/).
2. Pokud chcete nahrát video na server, stiskněte tlačítko nebo odkaz **Upload** (Nahrát).

    > [!NOTE]
    > Název videa nesmí být delší než 80 znaků.

    ![Nahrávání](./media/video-indexer-get-started/video-indexer-upload.png)

    Když se vaše video nahraje na server, začne ho Video Indexer indexovat a analyzovat.

    ![Nahráno](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Když Video Indexer dokončí analýzu, zobrazí se oznámení s odkazem na vaše video a krátkým popisem, co se ve videu našlo. Například: lidé, témata, OCR.

## <a name="see-also"></a>Viz také

Další podrobnosti najdete v tématu [videa pro nahrávání a indexování](upload-index-videos.md) .

Po nahrání a indexování videa můžete začít používat web [video indexer](video-indexer-view-edit.md) nebo [portál pro vývojáře video indexer](video-indexer-use-apis.md) , abyste viděli přehledy videa. 

[Začínáme používat rozhraní API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Další kroky

Podrobný Úvod najdete v našem [testovacím prostředí](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Na konci dílny budete mít dobrý přehled o druhu informací, které je možné extrahovat z videa a zvukového obsahu, budete připraveni k identifikaci příležitostí souvisejících s funkcí Content Intelligence, stoupání videa AI v Azure a Ukázka několika scénářů na Video Indexer.

