---
title: Nakonfigurovat metodu post pro Azure Media Services REST API volání
description: Tento článek popisuje, jak nakonfigurovat metodu post pro Media Services REST API volání.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: f55b3d5767f38c8fba2e79925f6f6e1e390aad2d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887789"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Nakonfigurovat metodu post pro Media Services REST API volání  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Projděte si nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

V tomto kurzu se dozvíte, jak nakonfigurovat **metodu post** , aby ji bylo možné použít k volání rozhraní REST api pro Azure Media Services (AMS). V tomto kurzu se dozvíte, jak importovat soubory prostředí a kolekcí do programu **post**. Kolekce obsahuje seskupené definice požadavků HTTP, které volají Azure Media Services (AMS) REST API. Soubor prostředí obsahuje proměnné, které kolekce používá.

Toto prostředí a kolekce se používají v článcích, které ukazují, jak dosáhnout různých úloh pomocí Azure Media Services rozhraní REST API.

## <a name="prerequisites"></a>Předpoklady

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další možnosti jsou: **Visual Studio Code** s pluginem REST nebo **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Vytvořte soubor. JSON, který obsahuje proměnné prostředí používané v kurzech AMS. Pojmenujte soubor (například **AzureMediaServices. postman_environment. JSON**). Otevřete soubor a vložte kód, který definuje prostředí pro publikování z [tohoto výpisu kódu](postman-environment.md). 
2. Spusťte nástroj **Postman**.
3. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
5. Vyhledejte a vyberte soubor **AzureMediaServices. postman_environment. JSON** .
6. Přidá se prostředí **AzureMedia** .
7. Zavřete dialogové okno.
8. Vyberte prostředí **AzureMedia** .

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurace kolekce

1. Vytvořte soubor. JSON, který obsahuje kolekci **post** , se všemi operacemi, které jsou potřeba k nahrání souboru do Media Services. Pojmenujte soubor (například **AzureMediaServicesOperations. postman_collection. JSON**). Otevřete soubor a vložte kód, který definuje kolekci **post** , z [tohoto výpisu kódu](postman-collection.md).
2. Kliknutím na **Import** importujte soubor kolekce.
3. Vyberte soubor **AzureMediaServicesOperations. postman_collection. JSON** .

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na článek o [odeslání assetů](media-services-rest-upload-files.md) .  
