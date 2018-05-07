---
title: "Konfigurace Postman pro volání REST API služby Azure Media Services"
description: "Zjistěte, jak nakonfigurovat Postman pro volání rozhraní REST API Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: juliako
ms.openlocfilehash: 72b110cac8d4945c958d760ff98e2da2f2796b62
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurace Postman pro volání rozhraní REST API Media Services

V tomto kurzu se dozvíte, jak nakonfigurovat **Postman** tak může sloužit k volání rozhraní API REST Azure Media Services (AMS). Tento kurz ukazuje, jak importovat prostředí a kolekce souborů do **Postman**. Kolekce obsahuje seskupené definice požadavků HTTP, které volání rozhraní API REST Azure Media Services (AMS). Soubor prostředí obsahuje proměnné, které jsou používány kolekce.

Toto prostředí a kolekce se používá v článcích, které ukazují, jak dosáhnout různé úlohy pomocí rozhraní API REST Azure Media Services.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [Postman](https://www.getpostman.com/) klienta REST provést rozhraní REST API uvedené v některé z kurzů k AMS REST. 

    Používáme **Postman** ale jakéhokoli REST nástroje bude vhodné. Další možnosti jsou: **Visual Studio Code** pomocí modulu plug-in REST nebo **webu Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Vytvořte soubor .json, který obsahuje systémové proměnné používané v kurzech AMS. Název souboru (například **AzureMediaServices.postman_environment.json**). Otevřete soubor a vložte kód, který definuje prostředí Postman z [tento kód výpis](postman-environment.md). 
2. Otevřete **Postman**.
3. Na pravé straně obrazovky vyberte **spravovat prostředí** možnost.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-env.png)
4. Z **spravovat prostředí** dialogové okno, klikněte na tlačítko **Import**.
5. Procházet a vybrat **AzureMediaServices.postman_environment.json** souboru.
6. **AzureMedia** prostředí je přidána.
7. Zavřete dialogové okno.
8. Vyberte **AzureMedia** prostředí.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfiguraci kolekce

1. Vytvořte soubor .json, který obsahuje **Postman** kolekce se všechny operace, které jsou potřebné pro nahrání souboru ke službě Media Services. Název souboru (například **AzureMediaServicesOperations.postman_collection.json**). Otevřete soubor a vložte kód, který definuje **Postman** kolekci z [tento kód výpis](postman-collection.md).
2. Klikněte na tlačítko **importovat** importovat soubor kolekce.
3. Vyberte **AzureMediaServicesOperations.postman_collection.json** souboru.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Další postup

Podívejte se [nahrát prostředky](media-services-rest-upload-files.md) článku.  
