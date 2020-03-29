---
title: Konfigurace volání rozhraní REST rozhraní API Postman pro Azure Media Services
description: Tento článek popisuje, jak nakonfigurovat Postman pro volání rozhraní REST API služby Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76694986"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurace pošťáka pro volání rozhraní REST V2 REST API  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Tento kurz ukazuje, jak nakonfigurovat **Postman** tak, aby jej lze použít k volání Azure Media Services (AMS) REST API. Výukový program ukazuje, jak importovat prostředí a soubory kolekce do **Postman**. Kolekce obsahuje seskupené definice požadavků HTTP, které volají Azure Media Services (AMS) REST API. Soubor prostředí obsahuje proměnné, které kolekce používá.

Toto prostředí a kolekce se používá v článcích, které ukazují, jak dosáhnout různých úloh s Azure Media Services REST API.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další alternativy jsou: **Visual Studio Code** s pluginem REST nebo **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Vytvořte soubor JSON, který obsahuje proměnné prostředí používané v kurzech AMS. Pojmenujte soubor (například **AzureMediaServices.postman_environment.json**). Otevřete soubor a vložte kód, který definuje postman prostředí z [tohoto výpisu kódu](postman-environment.md). 
2. Spusťte nástroj **Postman**.
3. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
5. Projděte a vyberte soubor **AzureMediaServices.postman_environment.json.**
6. Je přidáno prostředí **AzureMedia.**
7. Zavřete dialogové okno.
8. Vyberte prostředí **AzureMedia.**

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurace kolekce

1. Vytvořte soubor JSON, který obsahuje kolekci **Postman** se všemi operacemi, které jsou potřebné k nahrání souboru do služby Media Services. Pojmenujte soubor (například **AzureMediaServicesOperations.postman_collection.json).** Otevřete soubor a vložte kód, který definuje **kolekci Postman** z [tohoto výpisu kódu](postman-collection.md).
2. Kliknutím na **Import** importujte soubor kolekce.
3. Zvolte soubor **AzureMediaServicesOperations.postman_collection.json.**

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na článek [o nahracích datových zdrojů.](media-services-rest-upload-files.md)  
