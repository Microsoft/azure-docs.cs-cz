---
title: Konfigurace nástroje Postman pro volání REST API služby Azure Media Services
description: Zjistěte, jak konfigurace nástroje Postman pro volání REST API služby Media Services.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: d9db9cd83e0775ac71a852069cdf13c01fefea1d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981056"
---
# <a name="configure-postman-for-media-services-rest-api-calls-legacy"></a>Konfigurace nástroje Postman pro volání REST API služby Media Services (starší verze)

V tomto kurzu se dozvíte, jak nakonfigurovat **Postman** takže ho můžete použít k volání rozhraní REST API služby Azure Media Services (AMS). Tento kurz ukazuje, jak importovat prostředí a kolekce souborů do **Postman**. Kolekce obsahuje seskupené definice požadavků HTTP, které volání rozhraní REST API služby Azure Media Services (AMS). Prostředí obsahuje proměnné, které jsou používány v kolekci.

Toto prostředí a kolekce se používá v článcích, které ukazují jak dokončit různé úkoly pomocí REST API služby Azure Media Services.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další možnosti jsou: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Vytvořte soubor .json, který obsahuje proměnné prostředí používané v kurzech pro AMS. Název souboru (například **AzureMediaServices.postman_environment.json**). Otevřete soubor a vložte kód, který definuje Postman prostředí z [tento výpis kódu](postman-environment.md). 
2. Spusťte nástroj **Postman**.
3. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
5. Procházet a vybrat **AzureMediaServices.postman_environment.json** souboru.
6. **AzureMedia** prostředí je přidána.
7. Zavřete dialogové okno.
8. Vyberte **AzureMedia** prostředí.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurace kolekce

1. Vytvořte soubor .json, který obsahuje **Postman** kolekce se všechny operace, které jsou potřeba k nahrání souboru do Media Services. Název souboru (například **AzureMediaServicesOperations.postman_collection.json**). Otevřete soubor a vložte kód, který definuje **Postman** kolekce z [tento výpis kódu](postman-collection.md).
2. Kliknutím na **Import** importujte soubor kolekce.
3. Zvolte **AzureMediaServicesOperations.postman_collection.json** souboru.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Další postup

Podívejte se [si nahrávejte prostředky](media-services-rest-upload-files.md) článku.  
