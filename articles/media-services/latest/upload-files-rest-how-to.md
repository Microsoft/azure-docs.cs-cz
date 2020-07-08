---
title: Nahrání souborů do účtu Azure Media Services V3 pomocí REST | Microsoft Docs
description: Naučte se, jak získat mediální obsah do Media Services vytvořením a nahráním prostředků.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76705764"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Nahrání souborů do účtu Media Services V3 pomocí REST

V Media Services nahrajete digitální soubory do kontejneru objektů BLOB přidruženého k assetu. Entita [assetu](https://docs.microsoft.com/rest/api/media/operations/asset) může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech). Po nahrání souborů do kontejneru assetu se váš obsah bezpečně uloží do cloudu pro další zpracování a streamování.

V tomto článku se dozvíte, jak nahrát místní soubor pomocí REST.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků popsaných v tomto tématu je třeba provést následující kroky:

- Projděte si [koncept assetu](assets-concept.md).
- [Nakonfigurujte metodu post pro Azure Media Services REST API volání](media-rest-apis-with-postman.md).
    
    Nezapomeňte postupovat podle posledního kroku v tématu [získání tokenu Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Vytvoření prostředku

V této části se dozvíte, jak vytvořit nový prostředek.

1. Vyberte **Assets**  ->  **assety vytvořit nebo aktualizovat Asset**.
2. Stiskněte **Odeslat**.

    ![Vytvoření prostředku](./media/upload-files/postman-create-asset.png)

Zobrazí se **odpověď** s informacemi o nově vytvořeném prostředku.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Získat adresu URL SAS s oprávněním pro čtení i zápis 

V této části se dozvíte, jak získat adresu URL SAS, která byla vygenerována pro vytvořený Asset. Adresa URL SAS byla vytvořena s oprávněním pro čtení i zápis a lze ji použít k nahrání digitálních souborů do kontejneru assetů.

1. Vyberte **položku assets**  ->  **list adresy URL assetů**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/upload-files/postman-create-sas-locator.png)

Zobrazí se **odpověď** s informacemi o adresách URL prostředku. Zkopírujte první adresu URL a použijte ji k nahrání souboru.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrání souboru do úložiště objektů BLOB pomocí adresy URL pro nahrání

Použijte rozhraní API Azure Storage nebo sady SDK (například [úložiště REST API](../../storage/common/storage-rest-api-auth.md) nebo [sadu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Další kroky

[Kurz: kódování vzdáleného souboru na základě adresy URL a streamu pro video](stream-files-tutorial-with-rest.md)
