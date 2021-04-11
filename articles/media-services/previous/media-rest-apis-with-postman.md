---
title: Nakonfigurovat metodu post pro Azure Media Services REST API volání
description: Tento článek popisuje, jak nakonfigurovat metodu post pro Media Services REST API volání.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012203"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Nakonfigurovat metodu post pro volání REST API Media Services V2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

V tomto kurzu se dozvíte, jak nakonfigurovat **metodu post** , aby ji bylo možné použít k volání rozhraní REST api pro Azure Media Services (AMS). V tomto kurzu se dozvíte, jak importovat soubory prostředí a kolekcí do programu **post**. Kolekce obsahuje seskupené definice požadavků HTTP, které volají Azure Media Services (AMS) REST API. Soubor prostředí obsahuje proměnné, které kolekce používá.

Toto prostředí a kolekce se používají v článcích, které ukazují, jak dosáhnout různých úloh pomocí Azure Media Services rozhraní REST API.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další alternativy: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Vytvořte soubor. JSON, který obsahuje proměnné prostředí používané v kurzech AMS. Pojmenujte soubor (například **AzureMediaServices.postman_environment.json**). Otevřete soubor a vložte kód, který definuje prostředí pro publikování z [tohoto výpisu kódu](postman-environment.md). 
2. Spusťte nástroj **Postman**.
3. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Snímek obrazovky zobrazuje vybranou možnost spravovat prostředí.](./media/media-services-rest-upload-files/postman-create-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
5. Procházet a vybrat **AzureMediaServices.postman_environment.jsv** souboru.
6. Přidá se prostředí **AzureMedia** .
7. Zavřete dialogové okno.
8. Vyberte prostředí **AzureMedia** .

    ![Snímek obrazovky ukazuje vybrané prostředí AzureMedia.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurace kolekce

1. Vytvořte soubor. JSON, který obsahuje kolekci **post** , se všemi operacemi, které jsou potřeba k nahrání souboru do Media Services. Pojmenujte soubor (například **AzureMediaServicesOperations.postman_collection.json**). Otevřete soubor a vložte kód, který definuje kolekci **post** , z [tohoto výpisu kódu](postman-collection.md).
2. Kliknutím na **Import** importujte soubor kolekce.
3. Vyberte **AzureMediaServicesOperations.postman_collection.jsv** souboru.

    ![Snímek obrazovky se zobrazí dialogové okno pro IMPORT s vybranými možnostmi vybrané soubory.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na článek o [odeslání assetů](media-services-rest-upload-files.md) .  
