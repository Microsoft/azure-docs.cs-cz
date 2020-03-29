---
title: Nahrání souborů do účtu Azure Media Services v3 pomocí REST | Dokumenty společnosti Microsoft
description: Naučte se, jak získat mediální obsah do mediálních služeb vytvořením a nahráním datových zdrojů.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705764"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Nahrání souborů do účtu Media Services v3 pomocí rest

Ve službě Media Services nahrajete digitální soubory do kontejneru objektů blob přidruženého k datovému zdroji. Subjekt [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) může obsahovat video, zvuk, obrázky, sbírky miniatur, textové stopy a soubory skrytých titulků (a metadata o těchto souborech). Jakmile jsou soubory nahrány do kontejneru datového zdroje, váš obsah je bezpečně uložen v cloudu pro další zpracování a streamování.

Tento článek ukazuje, jak nahrát místní soubor pomocí REST.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto tématu, musíte:

- Zkontrolujte [koncept majetku](assets-concept.md).
- [Konfigurace postman pro Azure Media Services REST API volání](media-rest-apis-with-postman.md).
    
    Ujistěte se, že postupujte podle posledního kroku v tématu [Získat Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Vytvoření datového zdroje

V této části je uvedeno, jak vytvořit nový datový zdroj.

1. Vyberte **Datové zdroje** -> **Vytvořit nebo aktualizovat datový zdroj**.
2. Stiskněte **Odeslat**.

    ![Vytvoření datového zdroje](./media/upload-files/postman-create-asset.png)

Zobrazí **se odpověď** s informacemi o nově vytvořeném datovém zdroji.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Získání adresy URL SAS s oprávněními pro čtení a zápis 

Tato část ukazuje, jak získat adresu URL SAS, která byla vygenerována pro vytvořený datový zdroj. Adresa URL SAS byla vytvořena s oprávněními pro čtení a zápis a lze ji použít k nahrání digitálních souborů do kontejneru datových zdrojů.

1. Vyberte **položku Datové zdroje:** -> **Seznam adres URL datových zdrojů**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/upload-files/postman-create-sas-locator.png)

Zobrazí se **odpověď** s informacemi o adresách URL datového zdroje. Zkopírujte první adresu URL a použijte ji k nahrání souboru.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrání souboru do úložiště objektů blob pomocí adresy URL pro nahrávání

Použijte rozhraní API úložiště Azure nebo sady SDK (například [rozhraní REST API úložiště](../../storage/common/storage-rest-api-auth.md) nebo [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Další kroky

[Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - REST](stream-files-tutorial-with-rest.md)
