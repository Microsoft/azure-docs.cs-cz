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
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76694986"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Nakonfigurovat metodu post pro volání REST API Media Services V2  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](https://docs.microsoft.com/azure/media-services/latest/). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

V tomto kurzu se dozvíte, jak nakonfigurovat **metodu post** , aby ji bylo možné použít k volání rozhraní REST api pro Azure Media Services (AMS). V tomto kurzu se dozvíte, jak importovat soubory prostředí a kolekcí do programu **post**. Kolekce obsahuje seskupené definice požadavků HTTP, které volají Azure Media Services (AMS) REST API. Soubor prostředí obsahuje proměnné, které kolekce používá.

Toto prostředí a kolekce se používají v článcích, které ukazují, jak dosáhnout různých úloh pomocí Azure Media Services rozhraní REST API.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další alternativy: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Vytvořte soubor. JSON, který obsahuje proměnné prostředí používané v kurzech AMS. Pojmenujte soubor (například **AzureMediaServices.postman_environment.json**). Otevřete soubor a vložte kód, který definuje prostředí pro publikování z [tohoto výpisu kódu](postman-environment.md). 
2. Spusťte nástroj **Postman**.
3. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
5. Procházet a vybrat **AzureMediaServices.postman_environment.jsv** souboru.
6. Přidá se prostředí **AzureMedia** .
7. Zavřete dialogové okno.
8. Vyberte prostředí **AzureMedia** .

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurace kolekce

1. Vytvořte soubor. JSON, který obsahuje kolekci **post** , se všemi operacemi, které jsou potřeba k nahrání souboru do Media Services. Pojmenujte soubor (například **AzureMediaServicesOperations.postman_collection.json**). Otevřete soubor a vložte kód, který definuje kolekci **post** , z [tohoto výpisu kódu](postman-collection.md).
2. Kliknutím na **Import** importujte soubor kolekce.
3. Vyberte **AzureMediaServicesOperations.postman_collection.jsv** souboru.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na článek o [odeslání assetů](media-services-rest-upload-files.md) .  
