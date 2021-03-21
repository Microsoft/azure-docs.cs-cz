---
title: Nahrání souborů do účtu Media Services na webu Azure Portal | Dokumentace Microsoftu
description: Tento kurz vás provede postupem nahrání souborů do účtu Media Services na webu Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c2dc193d65ff1c85837477c0a8fd345f11d59bcd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009740"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Nahrání souborů do účtu Media Services na webu Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. Aktuální soubory pro nahrávání pomocí portálu najdete v tématu [použití portálu k nahrávání, kódování a streamování obsahu](../latest/manage-assets-quickstart.md).<br/>Podívejte se také na: [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Ve službě Azure Media Services nahráváte digitální soubory do prostředku. Prostředek může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech). Po nahrání souborů bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Služba Media Services má pro zpracování souborů omezení maximální velikosti souboru. Podrobnosti o omezeních velikosti souboru najdete v tématu [Kvóty a omezení služby Media Services](media-services-quotas-and-limitations.md).

K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Nahrání souborů
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení**  >  **prostředky**. Pak vyberte tlačítko **Nahrát**.
   
    ![Nahrání souborů](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zobrazí se okno **Nahrát asset videa**.
   
   > [!NOTE]
   > Služba Media Services pro nahrávání videí neomezuje velikost souboru.
 
3. Na svém počítači přejděte k videu, které chcete nahrát. Vyberte video a pak vyberte **OK**.  
   
    Spustí se nahrávání. Průběh můžete sledovat pod názvem souboru.  

Po dokončení nahrávání bude nový prostředek uvedený v podokně **Prostředky**. 

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [kódovat nahrané prostředky](media-services-portal-encode.md).

* Můžete také použít službu Azure Functions k aktivaci úlohy kódování při doručení souboru do nakonfigurovaného kontejneru. Další informace najdete v ukázce v tématu [Media Services: Integrace služby Azure Media Services se službou Azure Functions a Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).
