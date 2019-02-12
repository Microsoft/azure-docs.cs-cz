---
title: Nahrání souborů do účtu Azure Media Services pomocí rozhraní REST | Dokumentace Microsoftu
description: Další informace o získání mediálního obsahu do Media Services pomocí vytváření a nahrávání prostředky.
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
ms.openlocfilehash: a241f66adecbab1d0b1462f379d3765d6c1de252
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995386"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Nahrání souborů do účtu Azure Media Services pomocí rozhraní REST

Ve službě Media Services můžete digitální soubory nahrát do kontejneru objektů blob přidružený k assetu. [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) entita může obsahovat video, zvuk, obrázky, kolekci miniatur, text sleduje a skryté titulky soubory (a metadata o těchto souborech). Jakmile se soubory nahrají do kontejneru assetu, váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Tento článek popisuje, jak k nahrání místního souboru suing REST.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků popsaných v tomto tématu, budete muset:

- Kontrola [Asset koncept](assets-concept.md).
- [Konfigurace nástroje Postman pro volání REST API služby Azure Media Services](media-rest-apis-with-postman.md).
    
    Ujistěte se, že chcete postupovat podle poslední krok v tomto tématu [získat Azure AD na Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Vytvoření assetu

Tato část ukazuje, jak vytvořit nový prostředek.

1. Vyberte **prostředky** -> **vytvořit nebo aktualizovat prostředek**.
2. Stiskněte **Odeslat**.

    ![Vytvoření assetu](./media/upload-files/postman-create-asset.png)

Zobrazí **odpovědi** s informacemi o nově vytvořeného prostředku.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Získat adresu URL SAS s oprávněními pro čtení i zápis 

Tato část ukazuje, jak získat adresu URL SAS, který byl vygenerován pro vytvořený materiál. Adresa URL SAS byl vytvořen pomocí oprávnění pro čtení i zápis a slouží k digitální soubory nahrát do Assetu kontejneru.

1. Vyberte **prostředky** -> **seznam adres URL prostředku**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/upload-files/postman-create-sas-locator.png)

Zobrazí **odpovědi** s informacemi o adresy URL prostředku. Zkopírujte adresu URL první a použít ho k odeslání souboru.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrání souboru do úložiště objektů blob pomocí adresy URL pro odeslání

Pomocí rozhraní API služby Azure Storage nebo sady SDK (například [REST API pro Storage](../../storage/common/storage-rest-api-auth.md), [sady JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), nebo [sady .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Další postup

[Kurz: Vzdálený soubor na základě adresy URL kódování a streamování videa – REST](stream-files-tutorial-with-rest.md)
